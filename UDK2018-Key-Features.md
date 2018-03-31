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
* Centralized Configuration Management
* IOMMU-based DMA Protection
* Stack Guard, Heap Guard and NULL Pointer Detection
* Microsoft Visual Studio 2017 tool chain
* Hash-based incremental build
* Build time improvement using multi-threading in GenFds to generate FFS files

**********
**Note:** This page describes the  package notes and the differences based on previous UEFI Development Kit ([[UDK]]) UDK2017 Release.
* For a detailed list of Changes and updates,  See wiki page [[UDK2018]] Release 
**********