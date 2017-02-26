OVMF is an [[EDK II]] based project to enable [[UEFI]] support for Virtual Machines. OVMF contains sample UEFI firmware for [QEMU](http://www.qemu-project.org/) and [KVM](http://www.linux-kvm.org/).

License: [[BSD|BSD License]]

More information: [[OVMF FAQ]], [[How to build OVMF]], [[Boot Overview|OVMF-Boot Overview]], [[edk2-devel]]

Source repository: https://github.com/tianocore/edk2/tree/master/OvmfPkg

Additional Information

* http://www.linux-kvm.org/page/OVMF
* http://wiki.xen.org/wiki/OVMF
* Gerd Hoffmann’s OVMF builds: https://www.kraxel.org/repos/
  * These images are automatically built and track the latest OVMF code in the EDK II tree.
  * Some of these builds include a seabios CSM and can boot non-UEFI “legacy” operating systems. (Note: seabios is GPLv3 licensed.)
  * If your OS doesn’t work with RPM repositories, then you can manually download and decompress the RPM files under jenkins/edk2