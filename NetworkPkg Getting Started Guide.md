# **NetworkPkg Getting Started Guide**

## **Features List**

### IPv6 network stack

* **NetworkPkg/Ip6Dxe** - Ip6 driver, which produces
```
EFI_IP6_PROTOCOL  
EFI_IP6_CONFIG_PROTOCOL
```

* **NetworkPkg/Udp6Dxe** - Udp6 driver, which produces
```
EFI_UDP6_PROTOCOL
```

* **NetworkPkg/TcpDxe** - TCP combo driver, which produces
```
EFI_TCP4_PROTOCOL
EFI_TCP6_PROTOCOL
```
	1. There are two TCP drivers in EDK II, either Tcp4Dxe or TcpDxe could be used, but they should not be used at the same time.
		* Tcp4Dxe (MdeModulePkg/Universal/Network/Tcp4Dxe/Tcp4Dxe.inf) only support IPv4
		* TcpDxe (NetworkPkg/TcpDxe/TcpDxe.inf) support both IPv4 and IPv6.


* **NetworkPkg/Dhcp6Dxe** - DHCP6 driver, which produces
```
EFI_DHCP6_PROTOCOL
```
* **NetworkPkg/Mtftp6Dxe** - MTFTP6 driver, which produces
```
EFI_MTFTP6_PROTOCOL
```

### IPsec

* **NetworkPkg/IpSecDxe** - Ipsec driver, which produces
```
EFI_IPSEC2_PROTOCOL
EFI_IPSEC_CONFIG_PROTOCOL
```
	1. Supported features in IPsec:
		* Security Protocols: Encapsulating Security Payload (ESP)
		* IPsec Mode: Transport/Tunnel mode
    	* Encryption Algorithm: 3DES-CBC, AES-CBC
    	* Authentication Algorithm: HMAC\_SHA1\_96
    	* Authentication Method: Pre-shared Key, X509 Certificates
	2. After IPsec is enabled in both side, all inbound and outbound IP packet are processed by IPsec.

### PXE
* **NetworkPkg/UefiPxeBcDxe** - PXE driver, which produces
```
EFI_LOAD_FILE_PROTOCOL
EFI_PXE_BASE_CODE_PROTOCOL
```
Note: this driver supports both IPv4 and IPv6 network stack, and can't co-work with the UefiPxeBcDxe driver in MdeModulePkg.

### iSCSI
* **NetworkPkg/IScsiDxe** - iSCSi driver, which produces
```
EFI_ISCSI_INITIATOR_NAME_PROTOCOL
EFI_EXT_SCSI_PASS_THRU_PROTOCOL
EFI_AUTHENTICATION_INFO_PROTOCOL
```
Note: this driver supports both IPv4 and IPv6 network stack, and can't co-work with the IScsiDxe driver in MdeModulePkg.

### DNS
* **NetworkPkg/DnsDxe** - DNS driver, which produces
```
EFI_DNS4_PROTOCOL
EFI_DNS6_PROTOCOL
```

### HTTP Boot

* **NetworkPkg/HttpDxe** - HTTP driver, which produces
```
EFI_HTTP_PROTOCOL
```
* **NetworkPkg/HttpUtilitiesDxe** - HTTP utilities driver, which produces
```
EFI_HTTP_UTILITIES_PROTOCOL
```
* **NetworkPkg/HttpBootDxe** - HTTP Boot driver, which produces
```
EFI_LOAD_FILE_PROTOCOL
```

### Shell Application
* **Application/VConfig** - VLAN configuration Shell application
* **Application/IpsecConfig** - IPsec configuration Shell application
* Application/Ping6 - Ping6 has been integrated to Shell Spec 2.2, please use the command in ShellPkg.
* Application/Ifconfig6 - IfConfig6 has been integrated to Shell Spec 2.2, please use the command in ShellPkg.

### Notes
* UNDI, SNP, DPC and MNP drivers are components shared by IPv4 network stack and IPv6 network stack. These modules could be found in MdeModulePkg  except the UNDI driver. For UNDI driver, please contact the Card Vendor.

## FEATURES ENABLING

### Platform DSC file
```
[Defines]
  DEFINE NETWORK_ENABLE                        = TRUE
  DEFINE NETWORK_IP6_ENABLE                    = TRUE
  DEFINE NETWORK_ISCSI_ENABLE                  = TRUE
  DEFINE NETWORK_VLAN_ENABLE                   = TRUE
  DEFINE NETWORK_HTTP_BOOT_ENABLE              = TRUE
  DEFINE NETWORK_IPSEC_ENABLE                  = TRUE

[LibraryClasses]
  DpcLib|MdeModulePkg/Library/DxeDpcLib/DxeDpcLib.inf
  NetLib|MdeModulePkg/Library/DxeNetLib/DxeNetLib.inf
  IpIoLib|MdeModulePkg/Library/DxeIpIoLib/DxeIpIoLib.inf
  UdpIoLib|MdeModulePkg/Library/DxeUdpIoLib/DxeUdpIoLib.inf
  TcpIoLib|MdeModulePkg/Library/DxeTcpIoLib/DxeTcpIoLib.inf
  HttpLib|MdeModulePkg/Library/DxeHttpLib/DxeHttpLib.inf
  IntrinsicLib|CryptoPkg/Library/IntrinsicLib/IntrinsicLib.inf
  OpensslLib|CryptoPkg/Library/OpensslLib/OpensslLib.inf
  BaseCryptLib|CryptoPkg/Library/BaseCryptLib/BaseCryptLib.inf
  OpensslTlsLib|CryptoPkg/Library/OpensslLib/OpensslTlsLib.inf
  TlsLib|CryptoPkg/Library/TlsLib/TlsLib.inf

[Components]
!if $(NETWORK_ENABLE) == TRUE
  MdeModulePkg/Universal/Network/DpcDxe/DpcDxe.inf
  MdeModulePkg/Universal/Network/SnpDxe/SnpDxe.inf
  MdeModulePkg/Universal/Network/MnpDxe/MnpDxe.inf
  MdeModulePkg/Universal/Network/ArpDxe/ArpDxe.inf
  MdeModulePkg/Universal/Network/Dhcp4Dxe/Dhcp4Dxe.inf
  MdeModulePkg/Universal/Network/Ip4Dxe/Ip4Dxe.inf
  MdeModulePkg/Universal/Network/Mtftp4Dxe/Mtftp4Dxe.inf
  MdeModulePkg/Universal/Network/Tcp4Dxe/Tcp4Dxe.inf
  MdeModulePkg/Universal/Network/Udp4Dxe/Udp4Dxe.inf
!if $(NETWORK_IPSEC_ENABLE) == TRUE
  NetworkPkg/IpSecDxe/IpSecDxe.inf
!endif
!if $(NETWORK_HTTP_BOOT_ENABLE) == TRUE
  NetworkPkg/TlsDxe/TlsDxe.inf
  NetworkPkg/DnsDxe/DnsDxe.inf
  NetworkPkg/HttpDxe/HttpDxe.inf
  NetworkPkg/HttpUtilitiesDxe/HttpUtilitiesDxe.inf
  NetworkPkg/HttpBootDxe/HttpBootDxe.inf
!endif
!if $(NETWORK_IP6_ENABLE) == TRUE
  NetworkPkg/Ip6Dxe/Ip6Dxe.inf
  NetworkPkg/Dhcp6Dxe/Dhcp6Dxe.inf
  NetworkPkg/TcpDxe/TcpDxe.inf
  NetworkPkg/Udp6Dxe/Udp6Dxe.inf
  NetworkPkg/Mtftp6Dxe/Mtftp6Dxe.inf
!endif
!if $(NETWORK_IP6_ENABLE) == TRUE
  NetworkPkg/UefiPxeBcDxe/UefiPxeBcDxe.inf
!else
  MdeModulePkg/Universal/Network/UefiPxeBcDxe/UefiPxeBcDxe.inf
!endif
!if $(NETWORK_ISCSI_ENABLE) == TRUE
!if $(NETWORK_IP6_ENABLE) == TRUE
  NetworkPkg/IScsiDxe/IScsiDxe.inf
!else
  MdeModulePkg/Universal/Network/IScsiDxe/IScsiDxe.inf
!endif
!endif
!if $(NETWORK_VLAN_ENABLE) == TRUE
  MdeModulePkg/Universal/Network/VlanConfigDxe/VlanConfigDxe.inf
!endif
!endif
```
### Platform FDF file
```
!if $(NETWORK_ENABLE) == TRUE
  INF  MdeModulePkg/Universal/Network/SnpDxe/SnpDxe.inf
  INF  MdeModulePkg/Universal/Network/DpcDxe/DpcDxe.inf
  INF  MdeModulePkg/Universal/Network/MnpDxe/MnpDxe.inf
  INF  MdeModulePkg/Universal/Network/ArpDxe/ArpDxe.inf
  INF  MdeModulePkg/Universal/Network/Ip4Dxe/Ip4Dxe.inf
  INF  MdeModulePkg/Universal/Network/Udp4Dxe/Udp4Dxe.inf
  INF  MdeModulePkg/Universal/Network/Dhcp4Dxe/Dhcp4Dxe.inf
  INF  MdeModulePkg/Universal/Network/Mtftp4Dxe/Mtftp4Dxe.inf

  !if $(NETWORK_IPSEC_ENABLE) == TRUE
    INF  NetworkPkg/IpSecDxe/IpSecDxe.inf
  !endif

  !if $(NETWORK_IP6_ENABLE) == TRUE
    INF  NetworkPkg/Ip6Dxe/Ip6Dxe.inf
    INF  NetworkPkg/Dhcp6Dxe/Dhcp6Dxe.inf
    INF  NetworkPkg/Udp6Dxe/Udp6Dxe.inf
    INF  NetworkPkg/Mtftp6Dxe/Mtftp6Dxe.inf
  !endif

  !if $(NETWORK_HTTP_BOOT_ENABLE) == TRUE
    INF  NetworkPkg/DnsDxe/DnsDxe.inf
    INF  NetworkPkg/TlsDxe/TlsDxe.inf
    INF  NetworkPkg/HttpDxe/HttpDxe.inf
    INF  NetworkPkg/HttpUtilitiesDxe/HttpUtilitiesDxe.inf
    INF  NetworkPkg/HttpBootDxe/HttpBootDxe.inf
  !endif

  !if $(NETWORK_IP6_ENABLE) == TRUE
    INF  NetworkPkg/UefiPxeBcDxe/UefiPxeBcDxe.inf
    INF  NetworkPkg/TcpDxe/TcpDxe.inf
  !else
    INF  MdeModulePkg/Universal/Network/UefiPxeBcDxe/UefiPxeBcDxe.inf
    INF  MdeModulePkg/Universal/Network/Tcp4Dxe/Tcp4Dxe.inf
  !endif

  !if $(NETWORK_VLAN_ENABLE) == TRUE
    INF  MdeModulePkg/Universal/Network/VlanConfigDxe/VlanConfigDxe.inf
  !endif

  !if $(NETWORK_ISCSI_ENABLE) == TRUE
    !if $(NETWORK_IP6_ENABLE) == TRUE
      INF  NetworkPkg/IScsiDxe/IScsiDxe.inf
    !else
      INF  MdeModulePkg/Universal/Network/IScsiDxe/IScsiDxe.inf
    !endif
  !endif
!endif
```

## Using EDKII Network Stack on NT32

To validate network stack on NT32 platform, please refer to the document [UEFI Network Stack for EDK Getting Started Guide](https://sourceforge.net/projects/network-io/files/Documents/) to build the SnpNt32Io dynamic library.

## HTTP BOOT

For detailed description on UEFI HTTP Boot, see the "HTTP Boot" section in [UEFI Specification](http://www.uefi.org/specifications).

### HTTP Boot Getting Start

Please refer to the white paper [EDK II HTTP Boot Getting Started Guide](https://github.com/tianocore-docs/Docs/raw/master/White_Papers/EDKIIHttpBootGettingStartedGuide_0_7.pdf) for a step by step guide of the HTTP Boot enabling and server configuration in **corporate environment**.

Besides the standard DHCP parameters like the station IP, gateway and DNS server address, the EDK II HTTP Boot driver will use below extensions assigned by DHCP server in corporate environment.

| Tag Name | Tag # (DHCPv4) | Tag # (DHCPv6)| Length | Data Field |
| --- | --- | --- | --- |--- |
| Boot File | 'file' field in DHCP header or option 67 |59| Varies| Boot File URI String (eg. "http://Webserver/Boot/Boot.efi")|
| Class Identifier | 60 |16| 10 | "HTTPClient" |


### URI Configuration in Home Environment

Unlike the corporate network, in a typical home network only a standard DHCP server is available for host IP configuration assignment, the boot file URI need to be entered by user instead of the DHCP HTTPBoot extensions.
![Home Network Topology](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/Home.png)
EDK II HTTP Boot driver provides a configuration page for the boot file URI setup.
1. In the main page of Boot Manager Menu, enter [Device Manager] -> [Network Device List] -> Select a NIC device -> [HTTP Boot Configuration], set the HTTP boot parameters such as the boot option title, IP start version and the URI address as below.
	![HTTP Boot Configuration Page](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/URI_Address.PNG)

2. Save the configuration and back to the main page, enter [Boot Manager] menu as below, select the new created boot option to start the HTTP Boot.
	![Boot Manager Menu](https://github.com/tianocore/tianocore.github.io/wiki/Projects/NetworkPkg/Images/Boot_Option.PNG)

3. To delete the boot option, enter [Boot Maintenance Manager] -> [Boot Options] -> [Delete Boot Option].

### RAM Disk Boot from HTTP

Besides the UEFI formatted executable image, the downloaded boot file could also be an archive file (hard disk image) or an ISO image. The file should contain an UEFI-compliant file system and will be mounted as a RAM disk by HTTP Boot driver, to be proceeded in the subsequent boot process.

In EDKII HTTP Boot driver, the image type is identified by the filename extensions as below.

| Filename Extensions | Image Type |
| --- | --- |
| *.iso | Virtual CD Image |
| *.img | Virtual Disk Image |
| Others (typically *.efi) | UEFI Executable Image |

#### Note
To enable the RAM Disk boot support, the RamDiskDxe driver and the UefiBootManagerLib ([commit b1bb6f5](https://github.com/tianocore/edk2/commit/b1bb6f5961d82f30046e39e187a80556250f2bd1)) are also required.
```
[LibraryClasses]
  UefiBootManagerLib|MdeModulePkg/Library/UefiBootManagerLib/UefiBootManagerLib.inf
[Components]
  MdeModulePkg/Universal/Disk/RamDiskDxe/RamDiskDxe.inf
```
