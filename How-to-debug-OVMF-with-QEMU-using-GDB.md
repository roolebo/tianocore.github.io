# Debugging EDK II using OvmfPkg with QEMU and Linux GDB
This example will show how to debug a simple application built with OvmfPkg then using the QEMU and GDB to debug the UEFI Application.

### The following will use a UEFI_APPLICATION SampleApp.c as an example: 
1. Add your UEFI application to the OvmfPkgIa32.dsc (using IA32 )  example: `SampleApp/SampleApp.inf`  at the end of the `[Components]` section in the OvmfPkgIa32.dsc file.
2. Build OVMF for IA32 :  ` bash$ build -a IA32 -p OvmfPkg/OvmfPkgIa32.dsc -t GCC5`
3. Copy the output of SampleApp to the `hda-contents` directory similarly as shown in [[How-to-run-OVMF]] as a file system for QEMU.  This will be in a similar directory to the following : `/home/u-mypc/src/edk2/Build/OvmfIa32/DEBUG_GCC5/IA32`
```
   SampleApp.efi
   SampleApp.debug
   SampleApp (Directory)
```
4. Open a terminal(1) prompt in the `run-ovmf` directory as shown in [[How-to-run-OVMF]] with the ovmf.fd file copied to bios.bin.
5. invoke QEMU with the following command:
```
bash$ qemu-system-i386 -s  -pflash bios.bin -hda fat:rw:hda-contents -net none -debugcon file:debug.log -global isa-debugcon.iobase=0x402 
```
6. QEMU will load and boot to  a UEFI Shell prompt


### In the QEMU Window
Invoke your application.
```
Shell> fs0:
Fs0:\> SampleApp.efi
```
Open another terminal(2) prompt in the `run-ovmf` directory and check out the debug.log file. 
`bash$ cat debug.log`
```
InstallProtocolInterface: 5B1B31A1-9562-11D2-8E3F-00A0C969723B 6F0F028
Loading driver at 0x00006AEE000 EntryPoint=0x00006AEE756 SampleApp.efi
InstallProtocolInterface: BC62157E-3E33-4FEC-9920-2D3B36D750DF 6F0FF10

```
See that `Loading driver at 0x00006AEE000`  is the memory location where your UEFI Application is loaded. 
Keep this address in mind.
Additionally you might add a DEBUG statement to your application something like the following to get the entry point of your code. :
```
 DEBUG ((EFI_D_INFO, "My Entry point: 0x%08x\r\n", (CHAR16*)UefiMainMySampleApp )  );
```
Then when you `bash$ cat debug.log` you will also get the entry point for your application. In this example the entry point is `UefiMainMySampleApp`.

```
My Entry point: 0x06AEE496 
```
This is useful to double check your symbols are fixed up to the correct line numbers in your source file. Otherwise, the code you see in GDB may not be the code that is executing.

### Invoking GDB
In the terminal(2) prompt 
1. Change to the directory where the `hda-contents` is located
2. Invoke GDB with the source layout window using `bash$ gdb --tui` . At first there will be nothing in the source window.
3. Load your UEFI Application SampleApp.efi with the `file` command.
```
(gdb) file SampleApp.efi
Reading symbols from SampleApp.efi...(no debugging symbols found)...done.
```
4. Check where GDB has for .text and .data offsets with `info files` command.
```
(gdb) info files
Symbols from "/home/u-mypc/run-ovmf/hda-contents/SampleApp.efi".
Local exec file:
        `/home/u-mypc/run-ovmf/hda-contents/SampleApp.efi',
        file type pei-i386.
        Entry point: 0x756
        0x00000240 - 0x000028c0 is .text
        0x000028c0 - 0x00002980 is .data
        0x00002980 - 0x00002b00 is .reloc
```
5. We need to calculate our addresses for .text and .data section. Application is loaded under `0x00006AEE000 ` (loading driver point -  **NOT** **Entrypoint**) and we know text and data offsets. 
```
 text = 0x00006AEE000  +  0x00000240 = 0x06AEE240
 data = 0x00006AEE000  +  0x00000240 + 0x000028c0 = 0x06AF0B00 
```
6. Unload the .efi file 
```
(gdb) file
No executable file now.
No symbol file now.
```
7. Load the symbols with the fixed up address using your applications output .debug file:
```
(gdb) add-symbol-file SampleApp.debug 0x06AEE240 -s .data 0x06AF0B00 
add symbol table from file "SampleApp.debug" at

        .text_addr = 0x6aee240
        .data_addr = 0x6af0b00
(y or n) y
Reading symbols from SampleApp.debug...done.
```
8. Set a break point.  We have our entry point in the .inf file as: `UefiMainMySampleApp`
```
(gdb) break UefiMainMySampleApp 
Breakpoint 1 at 0x6aee496: file /home/u-uefi/src/edk2/SampleApp/SampleApp.c, line 40.
```
9. Attach the GDB debugger to QEMU:
```
(gdb) target remote localhost:1234
Remote debugging using localhost:1234
0x07df6ba4 in ?? ()
``` 
10. Continue in GDB
```
(gdb) c
Continuing.
```

### In the QEMU Window Invoke your application again
```
Shell> fs0:
Fs0:\> SampleApp.efi
```

The GDB will hit your break point in your UEFI application's entry point and you can begin to debug with source code debugging.

You can set more break points in your code with : `(gdb) break SampleApp.c:nn` : where _nn_ is the line of code in your .c file

Your GDB will look similar to this ( notice the `B+>` by line #40 in the source code is the entry point)


![](https://github.com/tianocore/tianocore.github.io/blob/master/images/GDB_QEMU.JPG)

After setting a few break points use `(gdb) c` to continue executing in the QEMU window until the next break point is hit. Then return to the GDB window to step, view local variables and continue debugging.

Use `(gdb) info locals` to view local variables


