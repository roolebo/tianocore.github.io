# Container for OSFC Workshops

This document describes how to install the container used for the following workshops at [[OSFC 2018]]:

* [Building Open Source UEFI Firmware with EDK II](https://osfc.io/talks/building-open-source-unified-extensible-firmware-interface-uefi-firmware-with-efi-development-kit-ii-edk-ii)
* [Debugging UEFI Firmware under Linux](https://osfc.io/talks/debugging-unified-extensible-firmware-interface-uefi-firmware-under-linux)

These containers are intended to simplify installation of tools and code across a variety of Linux distributions. If possible, please test the container installation prior to arriving at OSFC.

## Install the Container

You can download the container [here](https://firmware.intel.com/sites/default/files/edk2-ubuntu_docker_image.zip).

After downloading the container, install Docker if you have not done that already.

### Install Docker

Follow these instructions, based on distribution:  
* [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)  
* [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)  
* [OpenSUSE](https://en.opensuse.org/SDB:Docker)  
* [Arch Linux](https://wiki.archlinux.org/index.php/Docker)  
* [Install From Binary](https://docs.docker.com/install/linux/docker-ce/binaries/)

Once Docker is installed, follow the [post-install steps](https://docs.docker.com/install/linux/linux-postinstall/).

Before starting a Docker container, confirm that your user has been added to the `docker` group. This step is **required**.

### Install the Local EDK II Docker Image

The container can be installed locally with the `docker load` command. This assumes you have saved the docker image file as `edk2-ubuntu.docker`.

```bash
$> docker load -i edk2-ubuntu.docker
```

## Run the Container

To make sure code is available after exiting the container, please bind a working directory under home (`~/`) to a directory in the container. This example uses `~/workspace`. *Please note: the container assumes user id and group id are both 1000. This is the default on most distros.*

```bash
$> mkdir ~/workspace
$> cd ~/workspace
$> docker run --name edk2 --rm -it -v "$(pwd):/home/dockeruser/workspace" -e DISPLAY -v $HOME/.Xauthority:/home/dockeruser/.Xauthority --net=host edk2-ubuntu /bin/bash
```

The container will start in the EDK II workspace directory.

```bash
[edk2-ubuntu] workspace #
```

## Understanding the Container

The EDK II container is based on Ubuntu 16.04 and contains tools required for compiling tools and code. Example: ACPI tools are installed in `~/bin`  and `$PATH`.

The following options are used when running the container:

Option | Explanation
--- | ---
--rm | Automatically remove the container when it exits
-t | Allocate a pseudo-TTY
-i | Keep STDIN open even if not attached (interactive)
-v | Bind mount a volume
-e | Set environment variables
--net=host | Share the network stack with the host

The workspace is bound to the local home directory, so all work is saved outside the container (even after exit). X11 info is forwarded to enable proper execution of QEMU, and the network stack is shared with the host for X11 forwarding. Networking is helpful for remote access (SSH), but it does remove network isolation from the container.

### OpenSUSE Notes

If you are running OpenSUSE, X11 forwarding does not work by default. This can be resolved by executing the following command prior to running the container:

```bash
$> xhost local:root
```

There is also a DNS issue when using gdb debugging with openSUSE. When connecting to the QEMU target, use localhost instead of the hostname:

```bash
$> (gdb) target remote 127.0.0.1:1234
```

### SSH Notes

`git` can be used to commit/push code to remote repos outside the container. Performing these operations inside the container requires forwarding the SSH socket to the container. To enable this feature, add the following lines to the `docker run` command:

```bash
-v "$SSH_AUTH_SOCK:/tmp/ssh_auth_sock"
-e "SSH_AUTH_SOCK=/tmp/ssh_auth_sock"
```

### Opening Multiple Shell Windows

Opening multiple shell windows does not require running the `docker run` command multiple times. Simply use `docker exec` with the name of the running container:

```bash
# After `docker run`
$> docker exec -it edk2 bash
```

## OVMF Walkthrough

This example runs OVMF (x86-64) inside of the container.

First, clone EDK II into the workspace, build `BaseTools`, and edit the target config file. Note the `-j` option enables multithreaded build (4 threads in this example).

```bash
git clone https://github.com/tianocore/edk2.git
cd edk2
. ./edksetup.sh
make -C BaseTools -j 4
```

Expected output:

```bash
-------------------------------------------------------------------
Ran 263 tests in 0.597s
OK
make[1]: Leaving directory '/home/dockeruser/workspace/edk2/BaseTools/Tests'
make: Leaving directory '/home/dockeruser/workspace/edk2/BaseTools'
```

Now edit `Conf/target.txt` with the following values:

```
ACTIVE_PLATFORM       = OvmfPkg/OvmfPkgX64.dsc  
TARGET_ARCH           = X64  
TOOL_CHAIN_TAG        = GCC5  
```

The `build` command can now be executed without command line parameters:

```bash
build
```

The build process generates a binary UEFI firmware image:

```bash
Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd
```

Now run QEMU using the compiled firmware image:

```bash
qemu-system-x86_64 -pflash Build/OvmfX64/DEBUG_GCC5/FV/OVMF.fd -net none
```

## More Info

For more information or examples on building EDK II and writing applications, please refer to the [EDK II Wiki](https://github.com/tianocore/tianocore.github.io/wiki/Getting-Started-Writing-Simple-Application).

### Support Questions

IRC – stephano (freenode or OFTC)

Email: stephano.cetola@intel.com
