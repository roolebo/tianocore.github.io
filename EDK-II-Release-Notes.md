# edk2-stable201903 tag

## New Features
* [EDK II Release Planning](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Release-Planning)

## Wiki
* [C array structure PCD usage](https://bugzilla.tianocore.org/show_bug.cgi?id=1392)
* [ECC tool usage](https://github.com/tianocore/tianocore.github.io/wiki/ECC-tool)

## Update Notes
1. Use ShellPkg in Platform DSC/FDF to replace EdkShellBinPkg, because EdkShellBinPkg is removed.
2. Remove the using of PcdPeiCoreMaxFvSupported, PcdPeiCoreMaxPeimPerFv and PcdPeiCoreMaxPpiSupported
   in platform code as they have been removed for [Bug 1405](https://bugzilla.tianocore.org/show_bug.cgi?id=1405).
3. Remove the using of EmuVariableRuntimeDxe and use the merged Variable driver instead like below as 
   EmuVariableRuntimeDxe has been removed for [Bug 1323](https://bugzilla.tianocore.org/show_bug.cgi?id=1323).

  MdeModulePkg/Universal/Variable/RuntimeDxe/VariableRuntimeDxe.inf {
    <PcdsFixedAtBuild>
      gEfiMdeModulePkgTokenSpaceGuid.PcdEmuVariableNvModeEnable|TRUE
    <LibraryClasses>
      AuthVariableLib|MdeModulePkg/Library/AuthVariableLibNull/AuthVariableLibNull.inf
      TpmMeasurementLib|MdeModulePkg/Library/TpmMeasurementLibNull/TpmMeasurementLibNull.inf
      VarCheckLib|MdeModulePkg/Library/VarCheckLib/VarCheckLib.inf
  }