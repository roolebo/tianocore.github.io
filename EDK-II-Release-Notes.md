# edk2-stable201908 tag

## New Features
* [EDKII Planning](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Release-Planning)

## Wiki
* [UEFI Capsule on Disk Introducation](https://github.com/tianocore/tianocore.github.io/wiki/UEFI-Capsule-on-Disk-Introducation)

## Update Notes
1. Update code to use `CPU_FEATURE_THREE_STRIKE_COUNTER` instead of `CPU_FEATURE_THREE_STRICK_COUNTER` from RegisterCpuFeaturesLib.h as [BZ1642](https://bugzilla.tianocore.org/show_bug.cgi?id=1642) fixed the typo.

2. Removed IntelFrameworkPkg and IntelFrameworkModulePkg. If platforms still use the components in those packages please use the below substitutions:
```
IntelFrameworkModulePkg/Library/BaseUefiTianoCustomDecompressLib/BaseUefiTianoCustomDecompressLib.inf
==>
MdePkg/Library/BaseUefiDecompressLib/BaseUefiTianoCustomDecompressLib.inf

IntelFrameworkModulePkg/Library/LzmaCustomDecompressLib/LzmaCustomDecompressLib.inf
==>
MdeModulePkg/Library/LzmaCustomDecompressLib/LzmaCustomDecompressLib.inf

IntelFrameworkModulePkg/Library/PeiDxeDebugLibReportStatusCode/PeiDxeDebugLibReportStatusCode.inf
==>
MdeModulePkg/Library/PeiDxeDebugLibReportStatusCode/PeiDxeDebugLibReportStatusCode.inf

IntelFrameworkModulePkg/Library/GenericBdsLib/GenericBdsLib.inf
==>
Switch to the MdeModulePkg BDS MdeModulePkg/Universal/BdsDxe/BdsDxe.inf and drop the above library
```

3. Removed several legacy framework modules in PcAtChipsetPkg. Platforms can use the below substitutions:
```
PcAtChipsetPkg/8259InterruptControllerDxe/8259.inf
PcAtChipsetPkg/8254TimerDxe/8254Timer.inf
==>
PcAtChipsetPkg/HpetTimerDxe/HpetTimerDxe.inf
(Please note that platform/silicon codes may still need to mask 8259 interrupts to avoid unexpected interrupts being triggered.)

PcAtChipsetPkg/IsaAcpiDxe/IsaAcpi.inf
==>
Platform specific Super IO bus driver
(An example for OVMF platform can be referred at OvmfPkg/SioBusDxe/SioBusDxe.inf)
```

4. Removed --nt32 option for edksetup.bat since Nt32Pkg has been removed.
Added `VS2017 VS2015 VS2013 VS2012` tool chain options for edksetup.bat to set up different VS environment. 
For example: when your dev machine has installed VS2017 and VS2015, call `edksetup.bat VS2015` can set VS2015 build env.
Call `edksetup.bat` without any tool chain option, the highest version of VS tool env will be set.

TBD