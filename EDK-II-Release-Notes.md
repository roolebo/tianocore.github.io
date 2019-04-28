# edk2-stable201905 tag

## New Features
* [Update OpenSSL version to upcoming 1.1.1](https://bugzilla.tianocore.org/show_bug.cgi?id=1089)
* [Add new tool chain for LLVM/CLANG8.0](https://bugzilla.tianocore.org/show_bug.cgi?id=1603)
* [FeatureFlagExpression Support in LibraryClasses section of INF file](https://bugzilla.tianocore.org/show_bug.cgi?id=1446)
* [Delete EdkCompatibilityPkg from edk2/master](https://bugzilla.tianocore.org/show_bug.cgi?id=1103)
* [Remove IntelFrameworkPkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1604)
* [Remove IntelFrameworkModulePkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1605)
* [Remove .S assembly code for IA32 and X64 arch](https://bugzilla.tianocore.org/show_bug.cgi?id=1594)
* [Replace BSD 2-Clause License with BSD + Patent Licence](https://bugzilla.tianocore.org/show_bug.cgi?id=1373)
* [Recovery PEI BlockIO support for ATA device](https://bugzilla.tianocore.org/show_bug.cgi?id=1483)
* [Standardize EDK II PI root-of-trust verification implementation](https://bugzilla.tianocore.org/show_bug.cgi?id=1617)
* [Add PCD to Enabled/Disabled IPv4/IPv6 PXE Support in NetworkPkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1695)
* [Remove NetworkPkg/IpSecDxe](https://bugzilla.tianocore.org/show_bug.cgi?id=1697)
* [Add api to DebubLib to expose a print routine with VaList parameter](https://bugzilla.tianocore.org/show_bug.cgi?id=1395)
* [Introduce DebugPpi to save the image size with the debug message](https://bugzilla.tianocore.org/show_bug.cgi?id=1549)
* [ResetSystemLib Adds a new API ResetSystem](https://bugzilla.tianocore.org/show_bug.cgi?id=1460)
* [ResetUtilityLib Add a new API ResetSystemWithSubtype](https://bugzilla.tianocore.org/show_bug.cgi?id=1458)
* [Add support for get organization name to x509 in BaseCryptLib](https://bugzilla.tianocore.org/show_bug.cgi?id=1401)
* [Add support for checking x509 EKUs in BaseCryptLib](https://bugzilla.tianocore.org/show_bug.cgi?id=1402)
* [Add support for PKCS 1v2 RSAES-OAEP PKI encryption in BaseCryptLib](https://bugzilla.tianocore.org/show_bug.cgi?id=1403)
* [Remove ShellBinPkg from edk2/master](https://bugzilla.tianocore.org/show_bug.cgi?id=1675)

## Wiki
TBD

## Update Notes
1. PEIM DebugServicePei and library instance PeiDebugLibDebugPpi are added to save the PEIM Debug Image size. This can be enabled in platform DSC/FDF. Platform DSC is changed to include DebugServicePei and update DebugLib library instance. 
```
[LibraryClasses.Common.PEIM]
  DebugLib|MdeModulePkg/Library/PeiDebugLibDebugPpi/PeiDebugLibDebugPpi.inf

[Components]
  MdeModulePkg/Universal/DebugServicePei/DebugServicePei.inf {
    <LibraryClasses>
      DebugLib|MdeModulePkg/Library/PeiDxeDebugLibReportStatusCode/PeiDxeDebugLibReportStatusCode.inf
  }
```
Platform FDF also needs to be changed to include DebugServicePei and place it into apriori list.
```
[FV.PEIFV]
APRIORI PEI {
  INF  MdeModulePkg/Universal/DebugServicePei/DebugServicePei.inf
  }
INF  MdeModulePkg/Universal/DebugServicePei/DebugServicePei.inf
```

2. ShellBinPkg has been removed. Platform DSC/FDF need to use the ShellPkg directly.
Add shell application in platform fdf  file:
```
INF  ShellPkg/Application/Shell/Shell.inf
```
Add shell application in platform dsc file:
```
ShellPkg/Application/Shell/Shell.inf {
    <PcdsFixedAtBuild>
      gEfiShellPkgTokenSpaceGuid.PcdShellLibAutoInitialize|FALSE
    <LibraryClasses>
      NULL|ShellPkg/Library/UefiShellLevel2CommandsLib/UefiShellLevel2CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellLevel1CommandsLib/UefiShellLevel1CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellLevel3CommandsLib/UefiShellLevel3CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellDriver1CommandsLib/UefiShellDriver1CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellInstall1CommandsLib/UefiShellInstall1CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellDebug1CommandsLib/UefiShellDebug1CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellNetwork1CommandsLib/UefiShellNetwork1CommandsLib.inf
      NULL|ShellPkg/Library/UefiShellNetwork2CommandsLib/UefiShellNetwork2CommandsLib.inf
      ShellLib|ShellPkg/Library/UefiShellLib/UefiShellLib.inf
      ShellCommandLib|ShellPkg/Library/UefiShellCommandLib/UefiShellCommandLib.inf
      HandleParsingLib|ShellPkg/Library/UefiHandleParsingLib/UefiHandleParsingLib.inf
      BcfgCommandLib|ShellPkg/Library/UefiShellBcfgCommandLib/UefiShellBcfgCommandLib.inf
      FileHandleLib|MdePkg/Library/UefiFileHandleLib/UefiFileHandleLib.inf
      SortLib|MdeModulePkg/Library/UefiSortLib/UefiSortLib.inf
  }
```
Note: If  platform doesn’t have shell boot option after updating to use ShellPkg, please check platform code logic of registering shell boot option, make sure it use the correct UEFI Shell file GUID as below.
Shell file GUID: { 0x7C04A583, 0x9E3E, 0x4f1c, {0xAD, 0x65, 0xE0, 0x52, 0x68, 0xD0, 0xB4, 0xD1} }.

3. Remove IpSec driver and IpSecConfig application from NetworkPkg. Platform DSC/FDF should remove them. 
```
NetworkPkg/Application/IpsecConfig/IpSecConfig.inf
NetworkPkg/IpSecDxe/IpSecDxe.inf
```

