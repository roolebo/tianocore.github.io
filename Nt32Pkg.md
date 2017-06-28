# Nt32Pkg

Nt32Pkg implements [[NT32]], a [[UEFI]] simulation environment for Microsoft Windows. Unlike [[OVMF]], [[NT32]] does not support platform emulation or loading an operating system. NT32 is primarily used for UEFI pre-OS application prototyping and developer training.

NT32 will provide boot and runtime services for application development, mapping UEFI functions to Windows APIs. Boot services cannot be terminated in NT32.

The goals and features of the Nt32Pkg platform are similar to [[UnixPkg]] and [[EmulatorPkg]]. Our long term goal is to port [[EmulatorPkg]] to Microsoft Windows and retire Nt32Pkg.

Source Repository: https://github.com/tianocore/edk2/tree/master/Nt32Pkg 
