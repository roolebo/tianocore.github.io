# Overview

Host-based Firmware Analyzer (HBFA) enables advanced testing of UEFI drivers and UEFI Platform Initialization (PI) drivers in the developer’s OS environment. This test system was contributed to TianoCore edk2-staging branch by Intel in April 2019.

https://github.com/tianocore/edk2-staging/tree/HBFA 

# Background

Computer platform firmware is a critical element in the root-of-trust.  Firmware developers need a robust tool set to analyze and test firmware components, enabling detection of security issues prior to platform integration and helping to reduce validation costs.  HBFA allows developers to run open source advanced tools, such as fuzz testing, symbolic execution, and address sanitizers in a system environment.

# Additional Information

* [Using Host-based Analysis to Improve Firmware Resiliency](https://software.intel.com/en-us/blogs/2019/02/25/using-host-based-analysis-to-improve-firmware-resiliency) (blog)
* [Using Host-based Analysis to Improve Firmware Resiliency](https://software.intel.com/sites/default/files/managed/6a/4c/Intel_UsingHBFAtoImprovePlatformResiliency.pdf) (whitepaper)