[[PXE]] Boot is enabled by the [[EDK II]] network stack. If your platform does not have EDK II network stack built in yet, or you feel you miss any modules for enabling PXE boot, please refer to the “FEATURES ENABLING” section in the [[NetworkPkg Getting Started Guide]].

**NOTES:** The platform must support the UUID detection by reading table-based SMBIOS. Please refer the section 5.2.1 of PXE v2.1 specification for more details.
Below is an example of Windows WDS failure when SMBIOS is absent, the prompt message says "error occurred while communicating with the Windows Deployment Service server", but the failure actually because WDS client failed to read platform information from SMBIOS table. The firmware will also print debug warning message like "PXE: Failed to read system GUID from the smbios table!" in this situation.
![WDS Error](https://github.com/tianocore/tianocore.github.io/wiki/images/WDS-error.png "WDS Error")
