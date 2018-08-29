# Update the firmware and test a capsule

## Update MinnowBoard MAX Firmware from UEFI Capsules

* Copy the `Build/Vlv2TbltDevicePkg/Capsules` directory to a USB FLASH drive
* Connect USB FLASH Drive to MinnowBoard MAX
* Boot MinnowBoard MAX to the Boot Manager
* Boot the `EFI Internal Shell` boot option
* Mount the USB FLASH Drive (usually `FS1`)
* Use `cd` command to go to `Capsules/TestCert` directory
* Run the following command to apply all four capsules

```bash
CapsuleApp.efi Red.cap Green.cap Blue.cap MinnowMax.cap
```

* The MinnowBoard MAX should reboot and the four capsules are applied in the
  order listed.  The progress bar matches the color name of the capsule.
  MinnowMax.cap uses the color purple.  Once all capsules are processed, the
  MinnowBoard MAX should reboot again using the new firmware images.

## Generate and Test a UX BitMap Capsule

* Use bitmap editor to generate a BMP file.  Recommend resolution of 600 wide
  by 100 tell and either 24 or 32 bits per pixel.
* Save BMP file to USB FLASH drive
* Use CapsuleApp.efi to convert BMP file to a UX Capsule

```bash
CapsuleApp.efi -G MyImage.bmp -O MyImage.cap
```

* When updating firmware using capsules, add UX capsule to the list of capsules
  passed into CapsuleApp.efi.

```bash
CapsuleApp.efi MyImage.cap Red.cap Green.cap Blue.cap MinnowMax.cap
```

* When the capsules are processed the UX bitmap image should be displayed at the
  bottom of the screen.