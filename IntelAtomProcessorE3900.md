[[EDK II Platforms]] | [Intel® Processor Platforms](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Platforms#intel-processor-platforms)

***

# UEFI Firmware Project for Intel Atom® Processor E3900 Series Processor Platforms (Leaf Hill & MinnowBoard 3 Module)

This project is open source UEFI firmware, based on the TianoCore EDK II codebase, for the following platforms based on the Intel Atom® Processor E3900 Series processor (formerly Apollo Lake).

* Leaf Hill Customer Reference Board (CRB)
* MinnowBoard 3 Module (Pre-production Board, ship date TBD)

_Note: release 0.70 updates the codebase to use [[UDK2018]], and moves to a new branch of edk2-platforms on github ([devel-IntelAtomProcessorE3900](https://github.com/tianocore/edk2-platforms/tree/devel-IntelAtomProcessorE3900)). [[Releases prior to 0.69|MinnowBoard 3]] are based on different branches (devel-MinnowBoard3-UDK2017 & devel-MinnowBoard3)._

Developers can download pre-built UEFI firmware images, utilities, binary object modules, and project release notes. The open source firmware project is available from the TianoCore GitHub:

*  https://github.com/tianocore/edk2-platforms/tree/devel-IntelAtomProcessorE3900
*  https://firmware.intel.com/projects/IntelAtomProcessorE3900

## Reporting Firmware Issues

Please report any firmware issues in [TianoCore Bugzilla](https://bugzilla.tianocore.org/) using the following field values:

* Product: EDK2 Platforms
* Component: Minnowboard 3

See [[Reporting Issues]] for more information on TianoCore Bugzilla. 

## Supported Platforms

### MinnowBoard 3 Module

MinnowBoard 3 Module is the follow-on to the [[MinnowBoard Max|MinnowBoardMax]] & MinnowBoard Turbot platforms. MinnowBoard platforms offer low cost & commercially available open hardware based on Intel Architecture for hardware, software and firmware developers. Hardware availability is TBD.

MinnowBoard is an open source hardware enabler, encouraging platform experimentation and derivative designs. The project supports [Open Source Hardware Association](http://www.oshwa.org/) principles by making designs publicly available for the community so “anyone can study, modify, distribute, make, and sell the design or hardware based on that design.”

MinnowBoard 3 Module is based on the Intel Atom® processor E3900 Series platform, utilizing the Intel® Firmware Support Package (Intel® FSP) and open source UEFI from the TianoCore EDK II project. 

### Leaf Hill CRB

Leaf Hill refers to an Intel Customer Reference Board (CRB) using the [Intel Atom® Processor E3900 Series](https://www.intel.com/content/www/us/en/embedded/products/apollo-lake/overview.html) (formerly Apollo Lake). 

## Previous Codebase for MinnowBoard 3

Intel Atom® Processor E3900 Series Processor Platforms were originally supported by the [[MinnowBoard 3]] codebase. At release 0.70, this codebase was updated to use [[UDK2018]] and moved to a new branch with broader platform support.