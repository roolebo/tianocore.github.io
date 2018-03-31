# UDK2018 key features and changes

* Industry Standards & Public Specifications
  * UEFI 2.7a, 2.7 & 2.6b
    * Deprecate SMM Communication ACPI Table
    * Update Reset Notification Protocol
    * Add HII Popup Protocol
    * Add UEFI UFS DEVICECONFIG Protocol
    * Add Partition Information Protocol
    * Simplify Secure Boot Revocation & usage of VerifySignature
    * Add DNS device path
    * Add EFI HTTP Boot Callback Protocol
    * Add new data type to EFI Supplicant Protocol
    * Allow SetData to clear configuration in Ip4Config2/Ip6Config
  * UEFI PI 1.6, 1.5a & 1.5
    * Allow SEC to pass HOBs into PEI
    * Handle PEI PPI descriptor notifications from SEC
    * Add Pre-permanent memory page allocation
    * Add FV Extended Header entry containing used FV size
    * Add additional alignment in FFS file
    * Support standalone MM module generation
  * ACPI 6.2
    * Add ACPI IO Remapping Table (IORT) definition
* IOMMU-based DMA Protection
* Support Stack Guard, Heap Guard and NULL Pointer Detection
* Update OpenSSL version to the new 1.1.0g.
* Support CPU PPIN, LMCE and PROC_TRACE feature.
* Add OpalPassword solution without SMM device code.
* Update performance infrastructure to save perf entry in ACPI FPDT table.
* Remove TrEE libraries and drivers.
* Support Structure PCD as Centralized Configuration Management.
* Support Structure PCD asCentralized Configuration Management
* Microsoft Visual Studio 2017 tool chain
* Support hash-based build to improve the incremental build performance
* Build time improvement using multi-threading in GenFds to generate FFS files
*  Support XCODE5 tool chain build and boot functionality.

**********
**Note:** This page describes the  package notes and the differences based on previous UEFI Development Kit ([[UDK]]) UDK2017 Release.
* For a detailed list of Changes and updates,  See wiki page [[UDK2018]] Release 
**********