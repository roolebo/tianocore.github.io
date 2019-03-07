# edk2-stable201903 tag

## New Features
* [Python 3 migration](https://bugzilla.tianocore.org/show_bug.cgi?id=55)
* [BaseTool Suggestions for improving building performance](https://bugzilla.tianocore.org/show_bug.cgi?id=1288)
* [Delete IPv4 only TCP/iSCSI/PXE drivers in MdeModulePkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1278)
* [Remove EdkShellPkg from edk2/master](https://bugzilla.tianocore.org/show_bug.cgi?id=1107)
* [Remove EdkShellBinPkg from edk2/master](https://bugzilla.tianocore.org/show_bug.cgi?id=1108)
* [BaseTools: Support Array and C code style initialization in Structure PCD](https://bugzilla.tianocore.org/show_bug.cgi?id=1292)
* [Merge EmuVariable and Real variable driver](https://bugzilla.tianocore.org/show_bug.cgi?id=1323)
* [Remove DuetPkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1322)
* [Upgrade OpenSSL to 1.1.0j](https://bugzilla.tianocore.org/show_bug.cgi?id=1393)
* [Split the S3 phase device initialization codes from the OpalPassword PEI driver](https://bugzilla.tianocore.org/show_bug.cgi?id=1409)
* [Remove PcdPeiCoreMaxXXX PCDs](https://bugzilla.tianocore.org/show_bug.cgi?id=1405)
* [Remove unused tool logic in BaseTools C\Python](https://bugzilla.tianocore.org/show_bug.cgi?id=1350)
* [BaseTools: Enable component override functionality](https://bugzilla.tianocore.org/show_bug.cgi?id=1449)
* [Support PI1.7 EFI_PEI_CORE_FV_LOCATION_PPI](https://bugzilla.tianocore.org/show_bug.cgi?id=1524)
* [Remove unused tool chain configuration in tools_def.template](https://bugzilla.tianocore.org/show_bug.cgi?id=1377)
* [Add Security feature set support for ATA devices](https://bugzilla.tianocore.org/show_bug.cgi?id=1529)
* [SMM CET support](https://bugzilla.tianocore.org/show_bug.cgi?id=1521)
* [Add Wi-Fi Connection Manager to NetworkPkg](https://bugzilla.tianocore.org/show_bug.cgi?id=1492)
* [Standalone MM build of authenticated variable stack](https://bugzilla.tianocore.org/show_bug.cgi?id=1589)

## Wiki
* [CET in SMM](https://github.com/tianocore/tianocore.github.io/wiki/CET-in-SMM)
* [C array structure PCD usage](https://bugzilla.tianocore.org/show_bug.cgi?id=1392)
* [ECC tool usage](https://github.com/tianocore/tianocore.github.io/wiki/ECC-tool)
* [BaseTools Support Python2 and Python3](https://github.com/tianocore/tianocore.github.io/wiki/BaseTools-Support-Python2-Python3)

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
5. New working model [BZ1409](https://bugzilla.tianocore.org/show_bug.cgi?id=1409)
   has been adopted for the ATA and NVM Express OPAL devices S3 auto-unlock feature.
   The S3 phase hardware (ATA and NVM Express) initialization codes have been removed
   from the OpalPassword drivers. The OpalPasswordPei PEIM now will consume the
   Storage Security Command (SSC) PPI instances to unlock OPAL devices in S3. For
   the new working model, the following PEIMs:
  ```
  MdeModulePkg/Bus/Ata/AhciPei/AhciPei.inf
  MdeModulePkg/Bus/Pci/NvmExpressPei/NvmExpressPei.inf
  ```
   should be included by platforms so that SSC PPI instances will be produced for
   ATA and NVM Express devices respectively. Platforms also need to provide Host
   Controller PEIMs for ATA and NVM Express controllers. These PEIMs should respectively
   produce EDKII_ATA_AHCI_HOST_CONTROLLER_PPI and EDKII_NVM_EXPRESS_HOST_CONTROLLER_PPI
   in order to support the new working scheme. Lastly, please note that the PEIMs
   involved here will be executed during S3 resume. As a result, they may not be
   compressed, so there will be an impact to the image size.

6. Unused tool chain VS2003/VS2015, GCC44/GCC45/GCC46/GCC47, ELFGCC/UNIXGCC/CYGGCC, DDK3790, MYTOOLS
   are removed. Please use the latest VS2015 or GCC5 as the default tool chain. 
7. In case that a C function body contains the string of L'', L'\\"', L"\\"", L''' or L""", ECC tool running under python3 interpreter will report error with code 5005. Please ignore it for this error is false reported. For example, in ShellPkg\\Application\\Shell\\Shell.c, line 212 contains L"\\"". ```FirstQuote    = FindNextInstance (CmdLine, L"\"", TRUE)``` That line will cause ECC tool under python3 report error “The close brace should be at the very beginning of a line for the function [ContainsSplit].”, this error is a false report. ECC tool under python2 interpreter has no such issue.