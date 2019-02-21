# edk2-stable201903 tag

## New Features
* [EDK II Release Planning](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Release-Planning)

## Wiki
* [C array structure PCD usage](https://bugzilla.tianocore.org/show_bug.cgi?id=1392)
* [ECC tool usage](https://github.com/tianocore/tianocore.github.io/wiki/ECC-tool)

## Update Notes
1. Use ShellPkg in Platform DSC/FDF to replace EdkShellBinPkg, because EdkShellBinPkg is removed.
2. Remove the using of PcdPeiCoreMaxFvSupported, PcdPeiCoreMaxPeimPerFv and PcdPeiCoreMaxPpiSupported
   in platform code as they have been removed for [BZ1405](https://bugzilla.tianocore.org/show_bug.cgi?id=1405).
3. Remove the using of EmuVariableRuntimeDxe and use the merged Variable driver instead like below as 
   EmuVariableRuntimeDxe has been removed for [BZ1323](https://bugzilla.tianocore.org/show_bug.cgi?id=1323).
  ```
  MdeModulePkg/Universal/Variable/RuntimeDxe/VariableRuntimeDxe.inf {
    <PcdsFixedAtBuild>
      gEfiMdeModulePkgTokenSpaceGuid.PcdEmuVariableNvModeEnable|TRUE
    <LibraryClasses>
      AuthVariableLib|MdeModulePkg/Library/AuthVariableLibNull/AuthVariableLibNull.inf
      TpmMeasurementLib|MdeModulePkg/Library/TpmMeasurementLibNull/TpmMeasurementLibNull.inf
      VarCheckLib|MdeModulePkg/Library/VarCheckLib/VarCheckLib.inf
  }
  ```
4. Remove the TCP/iSCSI/PXE drivers in MdeModulePkg for [BZ1278](https://bugzilla.tianocore.org/show_bug.cgi?id=1278). Below components in NetworkPkg should be used to support both IPv4 and IPv6.
  ```
[Components]
  NetworkPkg/TcpDxe/TcpDxe.inf
  NetworkPkg/IScsiDxe/IScsiDxe.inf
  NetworkPkg/UefiPxeBcDxe/UefiPxeBcDxe.inf
  ```
5. New working model has been adopted for the ATA and NVM Express OPAL devices
   S3 auto-unlock feature. The S3 phase hardware initialization codes have been
   removed from the OpalPassword drivers. The OpalPasswordPei PEIM now will consume
   the Storage Security Command PPI instances to unlock OPAL devices in S3.
   More specifically, for ATA devices, a new PEIM:
  ```
  MdeModulePkg/Bus/Ata/AhciPei/AhciPei.inf
  ```
   is added for initializing the ATA devices in PEI and the PEIM will produce the
   Storage Security Command PPI for the managing devices.
   For NVM Express devices, the existing PEIM:
  ```
  MdeModulePkg/Bus/Pci/NvmExpressPei/NvmExpressPei.inf
  ```
   has been updated to produce the Storage Security Command PPI.
   The above 2 PEIMs should be included to support the new working model. Also,
   platform needs to provide Host Controller PEIMs, which produce EDKII_ATA_AHCI_HOST_CONTROLLER_PPI
   for ATA controllers and EDKII_NVM_EXPRESS_HOST_CONTROLLER_PPI for NVM Express
   controllers respectively, to support the new working scheme. [BZ1409](https://bugzilla.tianocore.org/show_bug.cgi?id=1409)