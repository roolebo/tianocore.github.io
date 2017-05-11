This page provides step-by-step instructions for setting up a [http://www.tianocore.org/edk2/ EDK II] build environment on Mac OS X systems using the Xcode development tools.  These steps have been verified with macOS Sierra Version 10.12.4

# Mac OS X Xcode
Download the latest version of [Xcode](https://developer.apple.com/xcode) (8.3.2) from the Mac App Store

Xcode is targeted at creating great Mac and iPhone applications. An extra tool, called mtoc, is required to convert from the OS X Mach-O image format to PE/COFF as required by UEFI.

Go to http://www.opensource.apple.com/ and click on the latest open source version of the developer tools (currently 8.2.1) and you will get a list of projects that can be downloaded. 

* Download the cctools project (currently cctools-895). 
* Expand the tar file (double click on it in Finder)
* Open a Terminal window to get a command line prompt.

To build `mtoc` you will need to copy an include directory from the LLVM project.

* Download http://llvm.org/releases/download.html#4.0
* Copy the include/llvm-c and include/llvm directories from LLVM into the cctools include directory, but do not overwrite include/llvm-c/Disassembler.h.

  ```
  cp cctools-895/include/llvm-c/Disassembler.h .
  cp -R llvm-4.0.0.src/include/llvm cctools-895/include/llvm
  cp -R llvm-4.0.0.src/include/llvm-c cctools-895/include/llvm-c
  cp Disassembler.h cctools-895/include/llvm-c
  ```

Then from the top cctools directory type:

```
make
```

The make will finish with an error message on the file `strip.c`. This is expected. Then do the following:

```
cd efitools
make
```

You have now built the command line application `mtoc.NEW`! Move it to a more useful location. 

```
sudo cp mtoc.NEW /usr/local/bin/mtoc
```

If this fails you probably don't have a local/bin directory under /usr. You need to add the directories by hand 

```
cd /usr
sudo mkdir local
cd local
sudo mkdir bin 
```
# Install NASM

In order to support EDK II firmware builds, the latest version of NASM from http://www.nasm.us/ must be installed.

```
brew install nasm
brew upgrade nasm
```

# Install ACPI Compiler

In order to support EDK II firmware builds, the latest version of the ASL compiler from https://acpica.org must be installed.

```
brew install acpica
brew upgrade acpica
```

# Install QEMU Emulator

On order to support running the OVMF platforms from the OvmfPkg, the QEMU emulator from http://www.qemu.org/ must be installed.

```
brew install qemu
brew upgrade qemu
```

# Update PATH environment variable

Tools installed using the `brew` command are placed in `usr/local/bin`.  The `PATH` environment variable must be updated so the newly installed tools are used instead of older pre-installed tools.

```
export PATH=/usr/local/bin:$PATH
```

# Verify tool versions

Run the following commands to verify the versions of the tools that have been installed.

```
nasm –v
iasl –v
qemu-system-x86_64 --version
mtoc
```
# Checkout edk2 From Source Control

Pick the location you want to down load the files to and `cd` to that directory:

```
cd ~/work
git clone https://github.com/tianocore/edk2.git
```

# Build from Command Line/Debug with gdb

Build the UnixPkg:

```
cd ~/work/edk2/UnixPkg
./build.sh
```

Debug the UnixPkg

```
./build.sh run

Building from: /Users/fish/work/edk2
using prebuilt tools
Reading symbols for shared libraries ...... done
Breakpoint 1 at 0xce84: file /Users/fish/work/edk2/UnixPkg/Sec/SecMain.c, line 1070.
(gdb) 
```

Type `r` at the gdb prompt (don't forget to hit carriage return) to boot the emulator. Ctrl-c in the terminal window will break in to gdb. bt is the stack backtrace command:

```
^C
Program received signal SIGINT, Interrupt.
0x92423806 in __semwait_signal ()
(gdb) bt
#0  0x92423806 in __semwait_signal ()
#1  0x9244f441 in nanosleep$UNIX2003 ()
#2  0x0000b989 in msSleep (Milliseconds=0x14) at /Users/fish/work/Migration/edk2/UnixPkg/Sec/UnixThunk.c:102
#3  0x0000acf5 in UgaCheckKey (UgaIo=0x2078d0) at /Users/fish/work/Migration/edk2/UnixPkg/Sec/UgaX11.c:380
#4  0x0000d8b7 in _GasketUintn () at /Users/fish/work/Migration/edk2/Build/Unix/DEBUG_XCODE32/IA32/UnixPkg/Sec/SecMain/OUTPUT/Ia32/Gasket.iii:63
#5  0x0000d801 in GasketUgaCheckKey (UgaIo=0x2078d0) at /Users/fish/work/Migration/edk2/UnixPkg/Sec/Gasket.c:406
#6  0x454a25fb in UnixUgaSimpleTextInWaitForKey (Event=0x45603610, Context=0x45382110) at /Users/fish/work/Migration/edk2/UnixPkg/UnixUgaDxe/UnixUgaInput.c:169
#7  0x45faad3a in CoreDispatchEventNotifies (Priority=0x10) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Event/Event.c:185
#8  0x45faa639 in CoreRestoreTpl (NewTpl=0x4) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Event/Tpl.c:114
#9  0x45f9f197 in CoreReleaseLock (Lock=0x45fb1024) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Library/Library.c:102
#10 0x45faabd6 in CoreReleaseEventLock () at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Event/Event.c:113
#11 0x45fab26c in CoreCheckEvent (UserEvent=0x45603210) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Event/Event.c:562
#12 0x45fab2db in CoreWaitForEvent (NumberOfEvents=0x1, UserEvents=0x45f94cc4, UserIndex=0x45f94cb8) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Event/Event.c:621
#13 0x49ce9557 in ?? ()
#14 0x49cf0344 in ?? ()
#15 0x49ce3bc2 in ?? ()
#16 0x49ce3ae1 in ?? ()
#17 0x45f9e4e3 in CoreStartImage (ImageHandle=0x49e31e10, ExitDataSize=0x45f94eec, ExitData=0x45f94ee8) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/Image/Image.c:1260
#18 0x4550cccc in BdsLibBootViaBootOption (Option=0x49ffa110, DevicePath=0x49ffa190, ExitDataSize=0x45f94eec, ExitData=0x45f94ee8) at /Users/fish/work/Migration/edk2/IntelFrameworkModulePkg/Library/GenericBdsLib/BdsBoot.c:382
#19 0x455252a9 in BdsBootDeviceSelect () at /Users/fish/work/Migration/edk2/IntelFrameworkModulePkg/Universal/BdsDxe/BdsEntry.c:214
#20 0x455255bc in BdsEntry (This=0x4552d01c) at /Users/fish/work/Migration/edk2/IntelFrameworkModulePkg/Universal/BdsDxe/BdsEntry.c:356
#21 0x45fad7e8 in DxeMain (HobStart=0x45f70010) at /Users/fish/work/Migration/edk2/MdeModulePkg/Core/Dxe/DxeMain/DxeMain.c:425
#22 0x45fadd1d in ProcessModuleEntryPointList (HobStart=0x42020000) at /Users/fish/work/Migration/edk2/Build/Unix/DEBUG_XCODE32/IA32/MdeModulePkg/Core/Dxe/DxeMain/DEBUG/AutoGen.c:287
#23 0x45f97773 in _ModuleEntryPoint (HobStart=0x42020000) at /Users/fish/work/Migration/edk2/MdePkg/Library/DxeCoreEntryPoint/DxeCoreEntryPoint.c:54
(gdb) 
```

# Build and Debug from Xcode
To build from the Xcode GUI open ~/work/edk2/UnixPkg/Xcode/xcode_project/xcode_project.xcodeproj. You can build, clean, and source level debug   from the Xcode GUI. You can hit the Build and Debug button to start the build process. You need to need to hit command-shift-B to show the output of the build. Click Pause to break into the debugger.

[[File:Xcode.jpg]]

The stack trace contains items that show as ?? since the default shell is checked in as a binary. `nanosleep$UNIX2003` and `__semwait_signal` are POSIX library calls and you do not get C source debug with these symbols.

# Source Level Debug Shell 

It is possible to get source level debug for the EFI Shell by pulling these projects from source control and building them.

Instructions for building and hooking in the shell are located in the [https://sourceforge.net/apps/mediawiki/tianocore/index.php?title=Gcc-shell gcc-shell] project.

Please note the gcc-shell and UnixPkg build separately, so if you update shell code you need to build the shell to see the changes. The following screen shot shows being able to source level debug the shell:

[[File:Xcode_good.jpg]]

# See Also

* [[Step-by-step instructions]]

