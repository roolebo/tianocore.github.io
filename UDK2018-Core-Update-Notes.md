# UDK2018 Core Update Notes
1.  GCC tool chain adds `--whole-archive` link option to detect the duplicated
    function or variable name. If the source code has such issue, the code
    needs to be fixed first. Or, platform can set link option to disable this
    checker in DSC file like below:<br>

    `[BuildOptions]`<br>
    ` GCC:*_*_*_DLINK_FLAGS = -Wl,--no-whole-archive`

2.  `MdePkg BaseLib` add new API `CalculateCrc32()` to calculate CRC32 value.
    If the source code has the same function name, it needs to be updated
    to use BaseLib one.

3.  Define `GLOBAL_REMOVE_IF_UNREFERENCED` as empty in `Base.h` for VS2013 or above
    compiler. If platform defines this MACRO as the different value, platform
    needs to update their code to remove this macro definition, and use it
    from Base.h. If platform has the duplicated global variable name from the
    different libraries, it may be exposed by this change. Platform needs to
    update the code to avoid the duplicated global variable name.

4.  To follow UEFI 2.7, variable driver is updated to do more check and expose
    an issue in `ShellPkg DmpStore.c`. Then, `DmpStore` command in old SHELL will
    not work. New SHELL binary or source needs to be used.

5.  The code to provide "TFTP" and "DP" shell commands is moved from
    ShellPkg/Library directory to ShellPkg/DynamicCommand directory.
    Platforms which use Shell source code needs to:

    a. Remove below two libraries' INF files reference from platform DSC file;

       `NULL|ShellPkg/Library/UefiShellTftpCommandLib/UefiShellTftpCommandLib.inf`,<br>
       `NULL|ShellPkg/Library/UefiDpLib/UefiDpLib.inf`

     b. Add dynamic commands for the two shell commands in DSC and FDF files;

       `ShellPkg/DynamicCommand/TftpDynamicCommand/TftpDynamicCommand.inf` <br>
       `ShellPkg/DynamicCommand/DpDynamicCommand/DpDynamicCommand.inf`


     c. Set `gEfiShellPkgTokenSpaceGuid.PcdShellLibAutoInitialize` as `FALSE`
       for Shell.inf and these two command INF in platform DSC.

6.  `SmbiosMeasurement` has been updated to skip measurement for OEM type.
    Platform code should measure OEM type by itself if required.

7.  Microcode update module has been moved from `UefiCpuPkg` to `IntelSiliconPkg`.
    Platform needs to update MicrocodeUpdate INF path in DSC/FDF like below.<br>
   ` UefiCpuPkg/Feature/Capsule/MicrocodeUpdateDxe/MicrocodeUpdateDxe.inf`
    `->`
    `IntelSiliconPkg/Feature/Capsule/MicrocodeUpdateDxe/MicrocodeUpdateDxe.inf`

8.  `PerformancePkg` is removed. Platform should use the "DP" command produced 
    by `ShellPkg/DynamicCommand/DpDynamicCommand. PcAtchipsetPkg/Library/`
    `AcpiTimerLib` is recommended to be used instead of `PerformancePkg/Library/`
    `TscTimerLib`. If the `overrided TscTimerLib` is still used, the definitions in 
    `PerformancePkg TscFrequency.h` and `GenericIch.h` need to be copied to 
    platform package.

9.  A new interface is introduced to `CpuExceptionHandlerLib`.<br>
        It must be implemented for any existing instance of this library otherwise
    the build will fail. This method can just call original `InitializeCpuExceptionHandlers`
    if there's no stack switch to setup for Stack Guard feature.

```
    EFI_STATUS
    EFIAPI
    InitializeCpuExceptionHandlersEx (
      IN EFI_VECTOR_HANDOFF_INFO            *VectorInfo OPTIONAL,
      IN CPU_EXCEPTION_INIT_DATA            *InitData OPTIONAL
      );
```

10. `DxeIpl` is updated to set all memory blocks used for page table as read-only.
    For those platforms providing their own implementation of `EFI_CPU_ARCH_PROTOCOL`,
    the `SetMemoryAttributes` method must be updated to clear `CR0.WP` before changing
    page attributes and re-set it afterwards.

11. PEI `SectionExtraction` PPI removes the hardcode alignment adjustment in GUIDED and 
    Compression section. If the leaf section in GUIDED and Compression section has the 
    alignment requirement, it needs to obviously specify its align in FDF file.

12. New performance library and DP application depends on ACPI50 FPDT table. Platform 
    needs to include `MdeModulePkg FirmwarePerformanceDataTablePei/FirmwarePerformanceDataTableDxe`
    `/FirmwarePerformanceDataTableSmm` drivers. And, if `PcAtChipsetPkg BaseAcpiTimerLib` is 
    used to catch the performance log in PEI phase, it needs to be changed to `PeiAcpiTimerLib`.

13. New `OpalPassword` solution remove `OpalPasswordSmm` and adds `OpalPasswordPei`.<br>
    Platform needs to exclude below lines in platform dsc and fdf. <BR>
      `OpalPasswordSupportLib|SecurityPkg/Library/OpalPasswordSupportLib/OpalPasswordSupportLib.inf`<br>
      `SecurityPkg/Tcg/Opal/OpalPasswordSmm/OpalPasswordSmm.inf`<br>
    Platform needs to update below line in platform dsc and fdf.<br>
  `    SecurityPkg/Tcg/Opal/OpalPasswordDxe/OpalPasswordDxe.inf`
      `->`
      `SecurityPkg/Tcg/Opal/OpalPassword/OpalPasswordDxe.inf`<br>
    Platform needs to add below line in platform dsc and fdf.<br>
     ` SecurityPkg/Tcg/Opal/OpalPassword/OpalPasswordPei.inf`<br>
    And platform needs to connect trusted storage and console to enable the new `OpalPassword` solution.
    S3 reserved memory size (for example, `PcdS3AcpiReservedMemorySize`) needs to be enlarged as 
    new `OpalPasswordPei` needs to allocate DMA buffer for DMA operation to unlock OPAL device.

14. New field Translation is added to `PCI_ROOT_BRIDGE_APERTURE` structure in `MdeModulePkg/`
    `Include/Library/PciHostBridgeLib.h`. Platform whose HOST address equals to DEVICE address needs 
    to initialize this field to 0.

15. All `TrEE` libraries and drivers are removed. A platform should use `Tcg2` libraries and drivers.<br>
    Left guid/header file/library/drivers are removed. They are required to be replaced by right ones.<br>
```
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
```

**********
**Note:** This page describes the Core package differences based on UEFI Development Kit ([[UDK]]) UDK2017 Release.
For a detailed list of Changes and updates  See  [[UDK2018]]  Release wiki page

**********
