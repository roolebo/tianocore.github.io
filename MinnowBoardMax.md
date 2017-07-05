[[EDK II Platforms]] | [Intel® Processor Platforms](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Platforms#intel-processor-platforms)

***

## MinnowBoard Max/Turbot

<img src="https://minnowboard.org/pages/default-page/Mb-Turbot-ADI-0008-150807_top.png" width="40%" height="40%" >

MinnowBoard Max & Turbot are low cost, commercially available, reference platforms for hardware, software and firmware developers who wish to work within an open environment. Design specifications and materials have been provided to the open community, encouraging platform experimentation and derivative designs.

The project is based on Intel® Atom™ processors. Technical details, schematics, and information on expansion boards (Lures) can be found at http://minnowboard.org

### UEFI Firmware

Documentation, binary images, and source build instructions are at http://firmware.intel.com/projects/minnowboard-max

* Binary Firmware Images ("UEFI BIOS"): 32-bit and 64-bit UEFI builds, with Debug and Release images. Includes flash update utilities and release notes.
* Binary Object Modules for which source code is not available due to the Intellectual property for both Intel silicon (microprocessor and chipset) and third party components.
* How to build Instructions for integrating the EDK II source tree (BSD license) with the Binary Object Modules to build firmware. See the latest **Release Notes .txt** file at http://firmware.intel.com/projects/minnowboard-max
   * How to build with FSP
   * How to configure Memory Parameters
   * How to enable fTPM feature
