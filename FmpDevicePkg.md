# FmpDevicePkg

This package provides an implementation of a Firmware Management Protocol
instance that supports the update of firmware storage devices using UEFI
Capsules.  The behavior of the Firmware Management Protocol instance is
customized using libraries and PCDs.

Based on content from the following branch:

https://github.com/Microsoft/MS_UEFI/tree/share/MsCapsuleSupport/MsCapsuleUpdatePkg

Library Classes
===============
* FmpDeviceLib - Provides firmware device specific services
  to support updates of a firmware image stored in a firmware
  device.
* CapsuleUpdatePolicyLib - Provides platform policy services
  used during a capsule update.
* FmpPayloadHeaderLib - Provides services to retrieve values
  from a capsule's FMP Payload Header.  The structure is not
  included in the library class.  Instead, services are
  provided to retrieve information from the FMP Payload Header.
  If information is added to the FMP Payload Header, then new
  services may be added to this library class to retrieve the
  new information.

PCDs set per module
====================
* PcdFmpDeviceSystemResetRequired - Indicates if a full
  system reset is required before a firmware update to a
  firmware devices takes effect
* PcdFmpDeviceTestKeySha256Digest - The SHA-256 hash of a
  PKCS7 test key that is used to detect if a test key is
  being used to authenticate capsules.  Test key detection
  is disabled by setting the value to {0}.
* PcdFmpDeviceProgressColor - The color of the progress bar
  during a firmware update.
* PcdFmpDeviceImageIdName - The Null-terminated Unicode
  string used to fill in the ImageIdName field of the
  EFI_FIRMWARE_IMAGE_DESCRIPTOR structure that is returned
  by the GetImageInfo() service of the Firmware Management
  Protocol for the firmware device.
* PcdFmpDeviceBuildTimeLowestSupportedVersion - The build
  time value used to fill in the LowestSupportedVersion field
  of the EFI_FIRMWARE_IMAGE_DESCRIPTOR structure that is
  returned by the GetImageInfo() service of the Firmware
  Management Protocol.
* PcdFmpDeviceProgressWatchdogTimeInSeconds - The time in
  seconds to arm a watchdog timer during the update of a
  firmware device.

PCDs set per module or for entire platform
==========================================
* PcdFmpDevicePkcs7CertBufferXdr - One or more PKCS7
  certificates used to verify a firmware device capsule
  update image.
* PcdFmpDeviceLockEventGuid - An event GUID that locks
  the firmware device when the event is signaled.