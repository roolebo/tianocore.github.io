## EDK II Platforms

Note: new platforms are being developed in the [edk2-platforms](https://github.com/tianocore/edk2-platforms) repository. Some older platforms still reside in the main [edk2](https://github.com/tianocore/edk2) repository.

### Virtual/Simulated Platforms

* [[OVMF]] - UEFI firmware support for the [QEMU](https://www.qemu.org/) open source machine emulator and virtualizer.
* [[Nt32Pkg]] - enabling UEFI application development in a Microsoft* Windows environment.
* [[EmulatorPkg]] - enable UEFI emulation within an OS environment.
* [[ArmVirtPkg]] - UEFI emulation for ARM processors.

### Intel® Processor Platforms

Recent Intel platform EDK II implementations follow a software architecture intended to aid in uniform delivery of Intel platforms called EDK II Minimum Platform. That architecture is described and maintained in the [EDK II Minimum Platform Specification draft](https://edk2-docs.gitbooks.io/edk-ii-minimum-platform-specification). Brief and practical information regarding the goals of a Minimum Platform and how to build are available in the Intel platform [Readme.md](https://github.com/tianocore/edk2-platforms/blob/master/Platform/Intel/Readme.md).

#### EDK II Minimum Platforms

* [[Kaby Lake MinPlatform]] - EDK II platform firmware on 7th Generation Intel® Core™ Processors and chipsets (formerly [Kaby Lake](https://ark.intel.com/products/codename/82879/Kaby-Lake) platforms).
* [[Whiskey Lake MinPlatform]] - EDK II platform firmware on 8th Generation Intel® Core™ Processors and chipsets (formerly [Whiskey Lake](https://ark.intel.com/content/www/us/en/ark/products/codename/135883/whiskey-lake.html) platforms).

#### Other Platforms

* [[Intel Atom® Processor E3900 Series|IntelAtomProcessorE3900]] - Designed for platforms using the [Intel Atom® Processor E3900 Series](https://www.intel.com/content/www/us/en/embedded/products/apollo-lake/overview.html) (formerly Apollo Lake). Includes the Leaf Hill CRB, Up Squared, and MinnowBoard 3 Module.
* [[Intel® Galileo Gen 2|Galileo]] - Arduino* certified,  Intel® Quark™ processor, built on fully open-source hardware
* [[MinnowBoard Max/Turbot|MinnowBoardMax]] - Open hardware platform with open source UEFI firmware, based on the Intel® Atom™ E3800 Series processor.
* [UEFI firmware for N-series Intel® Pentium® Processor and Intel® Celeron® Processor Families](https://firmware.intel.com/projects/braswell-uefi) (IA32 & x64)
* [[MinnowBoard]] - Intel® Atom™ E640 processor w/ IA32 UEFI firmware (deprecated)

### ARM Processor Platforms

* [[Beagle Board|Beagle Board Wiki]] -  low-power open-source hardware single-board computer produced by Texas Instruments.
* [[Omap35xxPkg]] provides UEFI support For Texas Instruments OMAP35xx based platforms.
