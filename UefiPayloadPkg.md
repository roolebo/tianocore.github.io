# UEFI_Payload

## What Is UEFI Payload
Unified Extensible Firmware Interface ([[UEFI]]) specification defines a set of interfaces for firmware modules interoperability and provides an interface for the operating systems to consume firmware services.
[[EDK II]] is a modern, feature-rich, cross-platform firmware development environment for the UEFI specification.


**UEFI Payload** is an EDK II based project to enable UEFI support for bootloaders like [Slim Bootloader](https://github.com/slimbootloader/slimbootloader) and coreboot. Bootloaders follow a modular approach for platform initialization (initialization stages) and OS boot logic (payload). The separation of platform initialization and boot logic allows the choice of different payloads.
UEFI Payload relies on the underlying boot firmware to initialize the platform and consumes the platform initialization information to be platform agnostic as much as possible. 

## UEFI Payload components

Comparing with bootloader that focuses on platform specific initialization, UEFI Payload focuses on boot logic using platform independent drivers. The platform specific information, e.g. memory map info and serial port settings, could be retrieved from bootloader to feed into generic drivers in UEFI Payload. 

The way to get platform information from bootloader is to use the [ParseLib](https://github.com/tianocore/edk2/blob/master/UefiPayloadPkg/Include/Library/BlParseLib.h). It gets information from bootloader and builds HOBs for the UEFI payload modules.

ParseLib instance [SblParseLib](https://github.com/tianocore/edk2/tree/master/UefiPayloadPkg/Library/SblParseLib) is used to parse information from Slim Bootloader and ParseLib instance [CbParseLib](https://github.com/tianocore/edk2/tree/master/UefiPayloadPkg/Library/CbParseLib) is used to parse information from coreboot. 

<img border="0" src="https://github.com/tianocore/tianocore.github.io/blob/master/images/UEFI-Payload.png" width="235" height="296">

## Build and Integration 

UEFI payload provides a generic payload for bootloaders to boot UEFI OS. In an ideal case, UEFI payload does not require any customization or any platform porting and can boot on different platforms by consuming the platform information from the bootloader. 
UEFI payload building is similar with other platform build in EDK2. See [BuildAndIntegrationInstructions.txt](https://github.com/tianocore/edk2/blob/master/UefiPayloadPkg/BuildAndIntegrationInstructions.txt) for more details.n

