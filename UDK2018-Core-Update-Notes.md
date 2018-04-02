# [[UDK2018]] Core Update Notes
1. The GCC tool chain adds `--whole-archive` link option to detect the duplicated function or variable name. If the source code has this issue, the code will need to be fixed first, or the platform can set a link option to disable this checker in the platform's DSC file as shown below:
```
[BuildOptions]
GCC:*_*_*_DLINK_FLAGS = -Wl,--no-whole-archive
```
2. The `MdePkg BaseLib` adds a new API `CalculateCrc32()` to calculate CRC32 value. If the source code has the same function name, it will need to be updated to use BaseLib one.

3. The Define `GLOBAL_REMOVE_IF_UNREFERENCED` is defined as empty in `Base.h` for Visual Studio 2013* (VS2013) and later compilers. If the platform defines this MACRO as the different value, the platform will need to update their code to remove this macro definition, and use it from Base.h. If the platform has the duplicated global variable name from the different libraries, it may be exposed by this change. Platforms will need to update the code to avoid the duplicated global variable name.

4. To follow UEFI 2.7, the variable driver is updated to do more check and expose an issue in `ShellPkg DmpStore.c`. Then the `DmpStore` command in old the SHELL will not work. The New UEFI SHELL binary or source needs to be used.

5. The code to provide `TFTP` and `DP` shell commands is moved from ShellPkg/Library directory to ShellPkg/DynamicCommand directory. Platforms which use Shell source code needs to:

a. Remove below two libraries' INF files reference from platform DSC file;
```
NULL|ShellPkg/Library/UefiShellTftpCommandLib/UefiShellTftpCommandLib.inf,
NULL|ShellPkg/Library/UefiDpLib/UefiDpLib.inf
```
b. Add dynamic commands for the two shell commands in DSC and FDF files;
```
ShellPkg/DynamicCommand/TftpDynamicCommand/TftpDynamicCommand.inf
ShellPkg/DynamicCommand/DpDynamicCommand/DpDynamicCommand.inf
```
c. Set` gEfiShellPkgTokenSpaceGuid.PcdShellLibAutoInitialize` as FALSE for Shell.inf and these two command INF in platform DSC.

6. The `SmbiosMeasurement` has been updated to skip measurement for OEM type. Platform code should measure OEM type by itself if required.

7. Microcode update module has been moved from `UefiCpuPkg` to `IntelSiliconPkg`. The Platform needs to update the MicrocodeUpdate INF path in DSC/FDF as below.
<pre>
UefiCpuPkg/Feature/Capsule/MicrocodeUpdateDxe/MicrocodeUpdateDxe.inf
-> IntelSiliconPkg/Feature/Capsule/MicrocodeUpdateDxe/MicrocodeUpdateDxe.inf
</pre>

8. The PerformancePkg is removed. Platforms should use the `DP` command produced by the `ShellPkg/DynamicCommand/DpDynamicCommand`. The `PcAtchipsetPkg/Library/AcpiTimerLib` is recommended to be used instead of the `PerformancePkg/Library/TscTimerLib`. If the overrided `TscTimerLib` is still used, the definitions in `PerformancePkg TscFrequency.h` and `GenericIch.h` will need to be copied to the platform package.

9. A new interface is introduced to `CpuExceptionHandlerLib`. It must be implemented for any existing instance of this library otherwise the build will fail. This method can just call original `InitializeCpuExceptionHandlers` if there is not a stack switch to setup for Stack Guard feature. The new interface is below:
```
EFI_STATUS
EFIAPI
InitializeCpuExceptionHandlersEx (
IN EFI_VECTOR_HANDOFF_INFO *VectorInfo OPTIONAL,
IN CPU_EXCEPTION_INIT_DATA *InitData OPTIONAL
);
```
10. The `DxeIpl` is updated to set all memory blocks used for page table as read-only. For those platforms providing their own implementation of `EFI_CPU_ARCH_PROTOCOL`, the `SetMemoryAttributes` method must be updated to clear `CR0.WP` before changing page attributes and re-set it afterwards.

11. The PEI `SectionExtraction` PPI removes the hardcode alignment adjustment in GUIDED and Compression section. If the leaf section in GUIDED and Compression section has the alignment requirement, it needs to obviously specify its align in FDF file.

12. The New performance library and DP application depends on the `ACPI50 FPDT` table. Platform needs to include `MdeModulePkg FirmwarePerformanceDataTablePei/FirmwarePerformanceDataTableDxe`
`/FirmwarePerformanceDataTableSmm` drivers. And, if `PcAtChipsetPkg BaseAcpiTimerLib` is
used to catch the performance log in PEI phase, it needs to be changed to `PeiAcpiTimerLib`.

13. The new `OpalPassword` solution removes the `OpalPasswordSmm` and adds the `OpalPasswordPei`.
Platforms will need to **exclude** the lines below in their platform dsc and fdf files.
```
OpalPasswordSupportLib|SecurityPkg/Library/OpalPasswordSupportLib/OpalPasswordSupportLib.inf
SecurityPkg/Tcg/Opal/OpalPasswordSmm/OpalPasswordSmm.inf
```
The Platform will need to update as in the line below for their platform dsc and fdf files.
```
SecurityPkg/Tcg/Opal/OpalPasswordDxe/OpalPasswordDxe.inf
-> SecurityPkg/Tcg/Opal/OpalPassword/OpalPasswordDxe.inf
```
The Platform will need to add the line below in their platform dsc and fdf files. <br>
`SecurityPkg/Tcg/Opal/OpalPassword/OpalPasswordPei.inf` <br>
The platform will also need to connect the trusted storage and console to enable the new `OpalPassword` solution. The `S3` reserved memory size (for example, `PcdS3AcpiReservedMemorySize`) will need to be enlarged as well as the new `OpalPasswordPei` will need to allocate a DMA buffer for DMA operations to unlock the OPAL device.

14. A new field Translation is added to `PCI_ROOT_BRIDGE_APERTURE` structure in `MdeModulePkg/Include/Library/PciHostBridgeLib.h`. A Platform whose HOST address equals to DEVICE address needs to initialize this field to 0.

15. All `TrEE` libraries and drivers are removed. A platform should use `Tcg2` libraries and drivers. Left guid/header file/library/drivers are removed. They are required to be replaced by right ones.

<pre>
    gTrEEConfigFormSetGuid                    <== gTcg2ConfigFormSetGuid
    gEfiTrEEPhysicalPresenceGuid              <== gEfiTcg2PhysicalPresenceGuid
    Include/Guid/TrEEConfigHii.h              <== Include/Guid/Tcg2ConfigHii.h
    Include/Guid/TrEEPhysicalPresenceData.h   <== Include/Guid/Tcg2PhysicalPresenceData.h
    Include/Library/TrEEPhysicalPresenceLib.h <== Include/Library/Tcg2PhysicalPresenceLib.h
    Include/Library/TrEEPpVendorLib.h         <== Include/Library/Tcg2PpVendorLib.h
    Library/TrEEPpVendorLibNull               <== Library/Tcg2PpVendorLibNull
    Library/DxeTrEEPhysicalPresenceLib        <== Library/DxeTcg2PhysicalPresenceLib
    Library/Tpm2DeviceLibTrEE                 <== Library/Tpm2DeviceLibTcg2
    Tcg/TrEEConfig                            <== Tcg/Tcg2Config
    Tcg/TrEEPei                               <== Tcg/Tcg2Pei
    Tcg/TrEEDxe                               <== Tcg/Tcg2Dxe
    Tcg/TrEESmm                               <== Tcg/Tcg2Smm
</pre>

**********
**Note:** This page describes the Core package differences based on UEFI Development Kit ([[UDK]]) [[UDK2017]] Release.
For a detailed list of Changes and updates See [[UDK2018]] Release wiki page

**********