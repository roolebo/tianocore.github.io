Back to [Capsule Based System Firmware Update](Capsule-Based-System-Firmware-Update)

# How to Generate Signing Keys using OpenSSL Command Line Utilities

These instructions generate a new self-signed X.509 Certificate Chain for signing UEFI Capsules, using [OpenSSL](https://www.openssl.org) command line utilities as an example.

> NOTE: These instructions only cover how to generate a new X.509 Certificate Chain.  It is up to the product owner to properly handle and protect a the cryptographic pair of private keys and public X.509 certificates used to sign and authenticate capsule-based system firmware update images.

The OpenSSL configuration and OpenSSL commands on this page were verified using the pre-built 32-bit [OpenSSL for Windows binaries, Version 1.0.2j Light](https://slproweb.com/products/Win32OpenSSL.html).  Other versions of OpenSSL may require different command flags or configuration settings.

# Setup OpenSSL Command Line Environment

> NOTE: The steps below are based on Microsoft Windows. Linux packages for OpenSSL will typically setup the environment correctly.

``` 
set OPENSSL_HOME=c:\OpenSSL-Win32\bin
set OPENSSL_CONF=%OPENSSL_HOME%\openssl.cfg
```

The `openssl.cfg` file must be reviewed to find the current CA path setting.

    [ CA_default ]
        dir = ./demoCA              # Where everything is kept

The `demoCA` directory may need to be initialized before our command sequence will work properly:

```
rmdir /s/q .\demoCA
mkdir .\demoCA
mkdir .\demoCA\newcerts
type NUL > .\demoCA\index.txt
echo 01 > .\demoCA\serial
```

# Generate a new Self-signed X.509 Certificate Chain

The following steps demonstrate how to generate a three layer certificate chain (RootCA -> IntermediateCA -> SigningCert) using OpenSSL.  A prefix should be used for all files referenced by OpenSSL commands.  The prefix used in this demonstration is `New`.  Many OpenSSL commands prompt the user for input, so the OpenSSL commands below should be run one at a time.

Private key files are password protected using the `-aes256` flag.  The sequence of commands provided in this demonstration will prompt for a password multiple times.

The `openssl req -new` command prompts the user for several pieces of information.  A unique value for `Common Name` **must** be provided for each of the three certificates generated or the process can not be completed. 

## Generate the Root Pair

### Generate a Root Key

```
openssl genrsa -aes256 -out NewRoot.key 2048
```

### Generate a self-signed Root Certificate

```
openssl req -new -x509 -days 3650 -key NewRoot.key -out NewRoot.crt
```

```
openssl x509 -in NewRoot.crt -out NewRoot.cer -outform DER
```

```
openssl x509 -inform DER -in NewRoot.cer -outform PEM -out NewRoot.pub.pem
```

## Generate the Intermediate Pair

### Generate the Intermediate Key

```
openssl genrsa -aes256 -out NewSub.key 2048
```

### Generate the Intermediate Certificate

```
openssl req -new -days 3650 -key NewSub.key -out NewSub.csr
```

```
openssl ca -extensions v3_ca -in NewSub.csr -days 3650 -out NewSub.crt -cert NewRoot.crt -keyfile NewRoot.key
```

```
openssl x509 -in NewSub.crt -out NewSub.cer -outform DER
```

```
openssl x509 -inform DER -in NewSub.cer -outform PEM -out NewSub.pub.pem
```

## Generate User Key Pair for Data Signing

### Generate User Key

```
openssl genrsa -aes256 -out NewCert.key 2048
```

### Generate User Certificate

```
openssl req -new -days 3650 -key NewCert.key -out NewCert.csr
```

```
openssl ca -in NewCert.csr -days 3650 -out NewCert.crt -cert NewSub.crt -keyfile NewSub.key
```

```
openssl x509 -in NewCert.crt -out NewCert.cer -outform DER
```

```
openssl x509 -inform DER -in NewCert.cer -outform PEM -out NewCert.pub.pem
```

Convert the Key and Certificate for signing.  The password is removed with `-nodes` flag for convenience in this demonstration.  If the `-nodes` flag is removed, the EDK II build will prompt for a password every time a capsule is signed.

```
openssl pkcs12 -export -out NewCert.pfx -inkey NewCert.key -in NewCert.crt
```

```
openssl pkcs12 -in NewCert.pfx -nodes -out NewCert.pem
```

## Verify Data Signing & Verification with the New X.509 Certificate Chain

### Create a Test File

```
echo Hello World > test.bin
```

### Generate Detached PKCS7 Signature from Signed Test File

```
openssl smime -sign -binary -signer NewCert.pem -outform DER -md sha256 -certfile NewSub.pub.pem -out test.bin.p7 -in test.bin
```

### Verify PKCS7 Signature of Signed Test File

```
openssl smime -verify -inform DER -in test.bin.p7 -content test.bin -CAfile NewRoot.pub.pem -out test.org.bin
```

# X.509 Certificate Chain Files

Once all the steps above have been completed successfully, the following generated files are used to sign and authenticate capsule based system firmware update images.

* **`NewRoot.cer`**: Public key that is used to configure the `gEfiSecurityPkgTokenSpaceGuid.PcdPkcs7CertBuffer` PCD value.  This PCD is used by EDK II firmware to authenticate a signed system firmware update image.
* **`NewRoot.pub.pem`**: Trusted public certificate that is passed in the `--trusted-public-cert` flag of the EDK II `Pkcs7Sign` utility.
* **`NewSub.pub.pem`**: Other public certificate that is passed in the `--other-public-cert` flag of the EDK II `Pkcs7Sign` utility.
* **`NewCert.pem`**: Signer private certificate that is passed in the `--signer-private-cert` flag of the EDK II `Pkcs7Sign` utility.

Back to [Capsule Based System Firmware Update](Capsule-Based-System-Firmware-Update)