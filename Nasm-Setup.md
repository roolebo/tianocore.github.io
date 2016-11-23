# Nasm

## Download Nasm
The NASM assembler is available from: 	http://www.nasm.us/

The link to the release notes for what is new in NASM 2.12 is at:  http://www.nasm.us/doc/nasmdocc.html

NASM 2.12 adds support for Codeview version 8 (cv8) which allows for source level debug of NASM sources when EDK II sources are built using Microsoft Visual Studio* 20xx tool chains  

## Nasm Prefix 
Nasm environment variable is used for the EDK II Build
If assembly code is used by the modules and the NASM assembler is used, the system environment variable, NASM_PREFIX must be set as shown below and must include the trailing backslash character:
<pre>
  C:\edk2\> set NASM_PREFIX =C:\nasm\
</pre>