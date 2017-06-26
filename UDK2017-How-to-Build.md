# How to Build UDK2017 
##                          HOW TO BUILD (WINDOWS SYSTEM)

The steps below are verified on Microsoft Windows 10 Enterprise*:

1.  Setup Build Environment
    1)  Install Microsoft Visual Studio 2015* in the build machine and make
        sure that AMD64 complier was selected when installing.
    2)  Download NASM 2.0.7 or later from http://www.nasm.us/ and install it to
        C:\Nasm. Make sure C:\Nasm is added to system environment variable 'PATH'
    3)  Set up for using GitHub for Windows. See:
    https://github.com/tianocore/tianocore.github.io/wiki/Getting-Started-with-EDK-II#github-help
    4)  Download and install Python2.7.x  https://www.python.org/ for building the BaseTools
        Default install directory is: C:/Python27

2.  Create the full Source Code directory for the UDK2017 release
    1)  Create a working space directory in the build machine, for example, C:\MyWorkspace

    2)  Download the UDK2017 release zip https://github.com/tianocore/edk2/archive/vUDK2017.zip 
        1) Extract files in [edk2-vUDK2017] to the working space directory C:\MyWorkspace.
    
    3) **OR**  Checkout the vUDK2017 Tag from GitHub with the following "git" command <br>
        1) run  `git clone  https://github.com/tianocore/edk2.git vUDK2017`
        2)  Move all files and folders under "vUDK2017" to "C:\MyWorkspace"

	 
    4)  **Optional** (See _Compile the BaseTools_ below) Checkout BaseTools binaries and copy them to BaseTools binary folder.  Warning the Windows* Binary tools are only valid for the tip of the  https://github.com/tianocore/edk2 repository.
         1)  `Cd C:\MyWorkspace`
         2)  Run `git clone https://github.com/tianocore/edk2-BaseTools-win32.git`
         3)  Enter folder edk2-BaseTools-win32
         4)  Run the command `git checkout 0e088c19ab31fccd1d2f55d9e4fe0314b57c0097`
         5)  `Cd C:\MyWorkSpace`
         6)  Rename this folder from edk2-BaseTools-win32 to win32, then copy the win32
           directory into the BaseTools/Bin directory under the workspace. 
           (e.g. "C:\MyWorkspace\BaseTools\Bin\")        

3.  Generate OpenSSL* Crypto Library
    1)  Open file "C:\MyWorkspace\CryptoPkg\Library\OpensslLib\OpenSSL-HOWTO.txt"
        and follow the instruction to install OpenSSL* for UEFI building.
        For this release, please use OpenSSL-1.1.0e.

4.  Compile the BaseTools (Skip if Optional Step 2.iv was done above) See: 
     https://github.com/tianocore/tianocore.github.io/wiki/Windows-systems#compile-tools
    1)  Open a Microsoft Visual Studio* command prompt, type `cd C:\MyWorkspace`
        to enter the  workspace directory
    2)  Compile the BaseTools C source tools  
<pre>
	set PYTHON_HOME=C:\Python27
        set EDK_TOOLS_PATH=%CD%\BaseTools
        BaseTools\toolssetup.bat Rebuild
</pre>

5.  Build Steps
    *** NT32 ***

    1)  Open a Microsoft Visual Studio* command prompt, type `cd C:\MyWorkspace`
        to enter the workspace directory
    2)  Use edksetup.bat command to initialize the working environment.
       `edksetup --nt32`
    3)  Type following command to build Nt32 platform
        `build -t VS2015x86`

---

##                         HOW TO BUILD (UNIX-LIKE SYSTEM)
The below steps are verified on Ubuntu 16.04 LTS Desktop*:

1.  Setup Build Environment
    1)  Follow instructions for setting up the build environment on tianocore.org.
        "https://github.com/tianocore/tianocore.github.io/wiki/Using-EDK-II-with-Native-GCC"
       2) Please notice that here the root is `"~/src/MyWorkspace"` instead
           of `"~/src/edk2"`
       3) Make sure BaseTools is built and required software like iASL compiler
           are installed. Some operations need switch to user "root" to execute.
         At Ubuntu, you can type `"sudo apt-get install git"` under terminal prompt "Cnt-Alt-T" to
           install git.
    2)  Install NASM 2.0.7 or later.
        At Ubuntu, you can type `"sudo apt-get install nasm"` under terminal prompt to
        install it.
        You can also download the source package from http://www.nasm.us/ and
        install it following the instruction on the website.
    3)  Install IASL 20150818 or later.
        At Ubuntu, you can type `"sudo apt-get install iasl"` under terminal prompt to
        install it.
        You can also download the source package from https://acpica.org/downloads
        and install it following the instruction on the website.
     4) Install the C Compiler- Ubuntu 16.04 LTS  you can use GNU C compiler (v5.4.0) 
        At Ubuntu, you can type `"sudo apt-get install gcc-5"` under terminal prompt to install it.


2.  Create the full Source Code directory for the UDK2017 release
    1)  Create a working space directory in the build machine, for example, ~/src/MyWorkspace

    2)  Download the UDK2017 release tar file from 
          https://github.com/tianocore/edk2/archive/vUDK2017.tar.gz
        1) Extract files in [edk2-vUDK2017] to the working space directory ~/src/MyWorkspace.
    3) **OR**  Checkout the vUDK2017 tag from GitHub with the following "git" command 
        1)  Run `"git clone https://github.com/tianocore/edk2.git vUDK2017"`
        2)  Move all files and folders under "vUDK2017" to "~/src/MyWorkspace"
	 
3.  Generate OpenSSL* Crypto Library
    1)  Open file "~/src/MyWorkspace/CryptoPkg/Library/OpensslLib/OpenSSL-HOWTO.txt"
        and follow the instruction to install OpenSSL* for UEFI building.
        For this release, please use OpenSSL-1.1.0e.

4.  Build Steps
    *** MdeModulePkg ***
    1)  Open a terminal and type `"cd ~/src/MyWorkspace"` to enter the workspace
        directory.
    2)  First build the BaseTools by typing 
        `"make -C BaseTools"`
    3)  Initialize the build environment by typing 
        `". edksetup.sh"`.
    4)  Type following command to build platforms using GCC v5.4.0
        `"build  -p MdeModulePkg/MdeModulePkg.dsc -t GCC5"`
