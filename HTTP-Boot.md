# **UEFI HTTP Boot**

For a detailed description on UEFI HTTP Boot, see the "HTTP Boot" section of the [UEFI Specification](http://www.uefi.org/specifications).

### HTTP Boot: Getting Started

Please refer to the white paper [EDK II HTTP Boot Getting Started Guide](https://github.com/tianocore-docs/Docs/raw/master/White_Papers/EDKIIHttpBootGettingStartedGuide_0_8.pdf) for a step by step guide of the HTTP Boot enabling and server configuration in **corporate environment**.

Additional information is available on the openSUSE Wiki: [UEFI HTTPBoot with OVMF](https://en.opensuse.org/UEFI_HTTPBoot_with_OVMF).

Besides the standard DHCP parameters like the station IP, gateway and DNS server address, the EDK II HTTP Boot driver will use the extensions below, assigned by the DHCP server in a corporate environment.

| Tag Name | Tag # (DHCPv4) | Tag # (DHCPv6)| Length | Data Field |
| --- | --- | --- | --- |--- |
| Boot File | 'file' field in DHCP header, or option 67 | 59 | Varies | Boot File URI String (eg. "http://Webserver/Boot/Boot.efi" or "http://Webserver/Boot/Boot.iso") |
| Class Identifier | 60 | 16 | 10 | "HTTPClient" |


### URI Configuration in Home Environment

Unlike the corporate network, the DHCP server in a typical home network environment is only available for host IP configuration assignment. This means the boot file URI must be entered by the user instead of the DHCP HTTPBoot extensions.
![Home Network Topology](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/Home.png)

EDK II HTTP Boot driver provides a configuration page for the boot file URI setup.

1. In the main page of Boot Manager Menu, enter [Device Manager] -> [Network Device List] -> Select a NIC device -> [HTTP Boot Configuration], set the HTTP boot parameters such as the boot option title, IP start version and the URI address as below.  
![HTTP Boot Configuration Page](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/URI_Address.PNG)
2. Save the configuration and back to the main page, enter [Boot Manager] menu as below, select the new created boot option to start the HTTP Boot.  
![Boot Manager Menu](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/Boot_Option.PNG)
3. To delete the boot option, enter [Boot Maintenance Manager] -> [Boot Options] -> [Delete Boot Option].

### RAM Disk Boot from HTTP

Besides the UEFI formatted executable image, the downloaded boot file could also be an archive file (hard disk image) or an ISO image. The file should contain an UEFI-compliant file system and will be mounted as a RAM disk by HTTP Boot driver, to be proceeded in the subsequent boot process.

In EDKII HTTP Boot driver, the image type is identified by the media type, or the filename extensions if "Content-Type" header is not present in the HTTP response message.

| Media Type | Filename Extensions | Image Type |
|---| --- | --- |
|[application/vnd.efi.iso](http://www.iana.org/assignments/media-types/application/vnd.efi-iso)|*.iso|Virtual CD Image|
|[application/vnd.efi.img](http://www.iana.org/assignments/media-types/application/vnd.efi-img)|*.img|Virtual Disk Image|
|[application/efi](http://www.iana.org/assignments/media-types/application/efi)|Others (typically *.efi)|UEFI Executable Image|

### RAM Disk Image Size
According to section 5.2.25.2 "System Physical Address (SPA) Range Structure" in ACPI 6.2 specification, UEFI firmware will try to allocate memory from **Reserved** memory range to store the downloaded boot image. So the maximum RAM disk image size depends on how much continuous reserved memory block the platform could provide.
> System physical address ranges described as address type Virtual CD or Virtual Disk shall be described as EFI Reserved Memory Type in UEFI GetMemoryMap API (AddressRangeReserved in E820 Table).

### Feature Enabling
UEFI HTTP boot is supported in UDK2017 release.
In previous UDK release, you may need to modify your code to enable the HTTP boot. Beside the HttpDxe/HttpBootDxe driver, the RamDiskDxe driver and the UefiBootManagerLib ([commit b1bb6f5](https://github.com/tianocore/edk2/commit/b1bb6f5961d82f30046e39e187a80556250f2bd1), [commit fb5848c](https://github.com/tianocore/edk2/commit/fb5848c588688d1e3cd3f175ff888549adddd024) and [commit 3a986a3](https://github.com/tianocore/edk2/commit/3a986a353db249e3ae128d47bff3a13c6e13a037)) are also required.
```
[LibraryClasses]
  UefiBootManagerLib|MdeModulePkg/Library/UefiBootManagerLib/UefiBootManagerLib.inf
[Components]
  MdeModulePkg/Universal/Disk/RamDiskDxe/RamDiskDxe.inf
```

Usually the UEFI HTTP Boot is forbidden due to security consideration (only HTTPS is allowed by default), please modify below PCD setting to **TRUE** in your platform to enable it.
```
  gEfiNetworkPkgTokenSpaceGuid.PcdAllowHttpConnections
```