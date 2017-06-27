# UDK2017 Core Update Notes 
1.  UserPhysicalPresent() behavior is changed in SecurityPkg\Library\PlatformSecureLibNull. <BR>
    It will return the value of new PCD PcdUserPhysicalPresence instead of TRUE.<BR>
    PcdUserPhysicalPresence's default value is FALSE.<BR>
    Please set correct PcdUserPhysicalPresence value in platform DSC file.<BR>
    For example, setting PcdUserPhysicalPresence to TRUE could keep the original
    behavior as before.
2.  The following PCDs are moved from IntelFrameworkModulePkg to MdeModulePkg.
    Please update module INF and platform DSC files accordingly.
    1) PcdFastPS2Detection
    2) PcdPs2KbdExtendedVerification
    3) PcdPs2MouseExtendedVerification
3.  CpuMpPei.inf is updated to consume MpInitLib and CpuExceptionHandlerLib.<BR>
    Please add the following library instances in platform DSC file, as below:<BR>
``` 
    [LibraryClasses.common.PEIM]
      MpInitLib|UefiCpuPkg/Library/MpInitLib/PeiMpInitLib.inf
      CpuExceptionHandlerLib|UefiCpuPkg/Library/CpuExceptionHandlerLib/PeiCpuExceptionHandlerLib.inf
``` 
4.  Add Tpm12DeviceLib instance for TcgDxe.inf in platform DSC file, as below: <BR>
```
    [LibraryClasses.common.DXE_DRIVER]
      Tpm12DeviceLib|SecurityPkg/Library/Tpm12DeviceLibDTpm/Tpm12DeviceLibDTpm.inf
```
5.  Structure RUNTIME_MEMORY_STATUSCODE_HEADER has been moved into MdeModulePkg
    public header file.
    Please remove the local definition from platforms.
6.  MdePkg/Include/IndustryStandard/SmBios.h is updated to add more definitions.
    Please remove all local duplicated definitions from platform.
7.  Please remove FSP_DATA_TABLE reference from FSP supported platform.
8.  ARRAY_SIZE is defined in MdePkg, please remove the duplicated definition from
    platform.
9.  Please add the following library instances in platform DSC file, as below:
```
    [LibraryClasses]
      PciSegmentLib|MdePkg/Library/BasePciSegmentLibPci/BasePciSegmentLibPci.inf
      FileExplorerLib|MdeModulePkg/Library/FileExplorerLib/FileExplorerLib.inf
```
10. Please add EFI_TTY_TERM_GUID support in platform UiApp driver. Please refer
    to this support in KabylakePlatSamplePkg/Features/UiApp/BootMaint. The below
    is one of updating for EFI_TTY_TERM_GUID support.
```
    ///
    /// Guid for messaging path, used in Serial port setting.
    ///
    GLOBAL_REMOVE_IF_UNREFERENCED EFI_GUID  TerminalTypeGuid[5] = {
      DEVICE_PATH_MESSAGING_PC_ANSI,
      DEVICE_PATH_MESSAGING_VT_100,
      DEVICE_PATH_MESSAGING_VT_100_PLUS,
      DEVICE_PATH_MESSAGING_VT_UTF8,
      EFI_TTY_TERM_GUID   ///< Added
    };
```
11. Platform BDS should invoke EfiBootManagerDispatchDeferredImages() just after
    gEfiDxeSmmReadyToLockProtocolGuid is installed. Please refer to the following
    code at quarkplatformpkg/library/platformbootmanagerlib/PlatformBootManager.c.<BR>
      `//` <BR>
     ` // Dispatch deferred images after EndOfDxe event and ReadyToLock installation.` <BR>
      `//` <BR>
     ` EfiBootManagerDispatchDeferredImages (); `<BR>
    If external Graphics card is chosen for display, Platform BDS should connect the
    external Graphics controller again to retrieve the GOP device path. It should add
    the GOP device path to "ConOut" EFI variable. <BR>
    If any PCI/PCIE devices containing UEFI Option ROM and not listed in
    ConIn/ConOut/ErrOut are connected before EndOfDxe/SmmReadyToLock, Platform BDS
    should connect these devices again after invoking
    `EfiBootManagerDispatchDeferredImages ()`.
12. After SmmReadyToLock, all SMI handlers should not access EfiLoaderCode/Data,
    EfiBootServicesCode/Data, EfiConventionalMemory and EfiACPIReclaimMemory memory
    regions. If any SMI handler access those memory ranges, #PF exception will be
    triggered. If that happened, the SMI handler should be updated to fix this issue.
    For those modules cannot be fixed, platform should remove them from platform FDF
    file. For example, Legacy USB driver should be removed from Kabylake platform.
13. HiiDatabaseDxe is enhanced to export HII data to be used by OS runtime later.
    All HII ExtractConfig() implementations should consider Request = NULL is legal
    input parameter.
    For example, it should check Request if is NULL before using it.<BR>
     ` - if (HiiIsConfigHdrMatch (Request, &mBootMaintGuid, mBootMaintStorageName)) {`<BR>
     `+ if ((Request != NULL) && HiiIsConfigHdrMatch (Request, &mBootMaintGuid, mBootMaintStorageName)) {` <BR>
    If some platform fails on S4 resume, please make sure PcdResetOnMemoryTypeInformationChange
    is set to TRUE by platform or set below PCD to FALSE to disable this feature
    in platform DSC file.<BR>
    `[PcdsFeatureFlag.common]`<BR>
     ` gEfiMdeModulePkgTokenSpaceGuid.PcdHiiOsRuntimeSupport|FALSE`
14. PlatformSecLib's SecPlatformInformation() should check input StructureSize value.
    It also should return correct size and correct status per PI specification.
15. The following macros defined in Pci22.h have been removed. Please do not use
    them in platform driver.<BR>
```
    #define DEVICE_ID_NOCARE    0xFFFF
    #define PCI_ACPI_UNUSED     0
    #define PCI_BAR_NOCHANGE    0
    #define PCI_BAR_OLD_ALIGN   0xFFFFFFFFFFFFFFFFULL
    #define PCI_BAR_EVEN_ALIGN  0xFFFFFFFFFFFFFFFEULL
    #define PCI_BAR_SQUAD_ALIGN 0xFFFFFFFFFFFFFFFDULL
    #define PCI_BAR_DQUAD_ALIGN 0xFFFFFFFFFFFFFFFCULL
    #define PCI_BAR_ALL         0xFF
```
16. RegisterTableEntry field in structure CPU_REGISTER_TABLE is updated to
    EFI_PHYSICAL_ADDRESS type.
17. UEFI 2.5 (Errata) spec updated the description of PortMultiplierPortNumber
    in SATA_DEVICE_PATH and the PortMultiplierPort parameter description in
    EFI_ATA_PASS_THRU_PROTOCOL to use 0xFFFF instead of 0 to indicate that
    there is no port multiplier.
    The consumer of SATA_DEVICE_PATH or EFI_ATA_PASS_THRU_PROTOCOL needs to
    re-examine and update its usage accordingly.
    For example, a case was met in a server platform: <BR>
      `DevicePath = ConvertTextToDevicePath (L"PciRoot(0x0)/Pci(0x17,0x0)/Sata(0x0,0x0,0x0)");`<BR>
    should be updated to<BR>
      `DevicePath = ConvertTextToDevicePath (L"PciRoot(0x0)/Pci(0x17,0x0)/Sata(0x0,0xFFFF,0x0)");`
18. The version of TPM 2.0 ACPI table has been updated to Rev 4. But for Windows 8.1,
    TPM 2.0 discoverability is only supported with TPM 2.0 ACPI table Rev 3.
    Please change the ACPI version from Rev 4 to Rev 3 for Windows 8.1 WHCK tests.
19. PrintLib APIs UnicodeValueToString() and AsciiValueToString() are deprecated.
    And their safe counterparts UnicodeValueToStringS() and AsciiValueToStringS()
    are added.
    If the macro "DISABLE_NEW_DEPRECATED_INTERFACES" is defined in platform, then <BR>
    UnicodeValueToString() and AsciiValueToString() should be replaced with
    UnicodeValueToStringS() and AsciiValueToStringS() respectively.
20. MtrrLib was updated to check the MTRR precedence.<BR>
    If a platform uses below deprecated macros defined in UefiCpuPkg/Include/Library/MtrrLib.h,
    assertion might be triggered from MtrrLibPrecedence().<BR>
     ` #define  MTRR_LIB_MSR_VALID_MASK                     0xFFFFFFFFFULL` <BR>
     ` #define  MTRR_LIB_CACHE_VALID_ADDRESS                0xFFFFFF000ULL` <BR>
    Please refer to MtrrLibInitializeMtrrMask() in MtrrLib to calculate the bit
    mask and address mask.
21. SmmIoLib has been added to perform MMIO range checks.
    If platforms have their own SmmIoLib, which is a duplication with the core
    IoLib and PciLib, please replace the usages of platform SmmIoLib with core
    IoLib and PciLib.
22. Please use the following instance for SecurityPkg/Tcg/Tcg2Smm/Tcg2Smm.inf.<BR>
    `<LibraryClasses>` <BR>
 `     Tpm2DeviceLib|SecurityPkg/Library/Tpm2DeviceLibTcg2/Tpm2DeviceLibTcg2.inf`

**********
**Note:** This page describes the Core package differences based on UEFI Development Kit ([[UDK]]) UDK2015 Release.
For a detailed list of Changes and updates  See  [[UDK2017]]  Release wiki page

**********