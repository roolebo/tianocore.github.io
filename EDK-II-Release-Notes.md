# edk2-stable201905 tag

## New Features
* [EDKII Planning](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Release-Planning)

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

2. ShellBinPkg has been removed. Shell binaries can be download from the Assets section in edk2-stable201905 release page. Platform can also use ShellPkg directly, and update platform dsc/fdf file as below.
Add shell application in platform fdf file:
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

4. UefiDecompressLib instance `IntelFrameworkModulePkg/Library/BaseUefiTianoCustomDecompressLib` has been merged into `MdePkg/Library/BaseUefiDecompressLib`. If platform still use the one in IntelFrameworkModulePkg, please update it to use the one in MdePkg. 
```
UefiDecompressLib|IntelFrameworkModulePkg/Library/BaseUefiTianoCustomDecompressLib/BaseUefiTianoCustomDecompressLib.inf
==>
UefiDecompressLib|MdePkg/Library/BaseUefiDecompressLib/BaseUefiTianoCustomDecompressLib.inf
```

5. Removed EDK Compatibility support. If platform still use the `PcdFrameworkCompatibilitySupport` or framework VFR, please remove or update the related code logic or source file.

6. Network Module and Libraries are moved from MdeModulePkg to NetworkPkg. The platform DSC/FDF needs to include Network segment files to enable Network features instead of including the group of network modules. Those segment files are included into the different sections in DSC/FDF as below. If the module consumes Network library class, its INF needs to make sure `NetworkPkg\NetworkPkg.dec` in `[Packages]` section.
```
Platform.dsc:
[Defines]
!include NetworkPkg/NetworkDefines.dsc.inc

[PcdsFixedAtBuild]
!include NetworkPkg/NetworkPcds.dsc.inc

[LibraryClasses]
!include NetworkPkg/NetworkLibs.dsc.inc

[Components]
!include NetworkPkg/NetworkComponents.dsc.inc

Platform.fdf:
[FV.DXEFV]
...
!include NetworkPkg/Network.fdf.inc
```

7. Openssl has been updated to new 1.1.1b version. Compared to previous version, new version openssl increases the image size for the driver that consumes CryptoLib. Platform FDF file may reserve more space in FV image to contain them. 