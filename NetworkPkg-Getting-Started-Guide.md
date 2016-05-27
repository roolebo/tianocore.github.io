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

Note: There are two TCP drivers in EDK II, either Tcp4Dxe or TcpDxe could be used, but they should not be used at the same time.

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

## UEFI HTTP BOOT

Please refer to the [UEFI HTTP Boot](https://github.com/tianocore/tianocore.github.io/wiki/HTTP-Boot) page.
