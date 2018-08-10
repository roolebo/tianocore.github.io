# Performance Infrastructure 
User can use the Perf macros in edk2/MdePkg/Include/Library/PerformanceLib.h to log the performance information.<BR>
The performance information are collected in FPDT record format directly by performance libraries and then saved in ACPI Firmware Performance Data Table(FPDT).<BR>
Thus the performance data can be dumped both in UEFI Shell and OS by tools to parse FPDT in ACPI table.

# Performance Enable
 **(1) Make sure the Library instances are used correctly in different phase.**

 **(2) Make sure ACPI is enabled on your platform.**

 **(3) Make sure report progress status code is enabled on your platform.<BR>
       It's required because performance data are collected through reporting progress status code.<BR>
       You will not get the performance data if it's not enabled.<BR>
       (PcdReportStatusCodePropertyMask: BIT0-Enable Progress Code)**

 **(4) Set PcdPerformanceLibraryPropertyMask=1.**

 **(5) If you want to use DP command in shell to dump performance data,<BR>
       you can add ShellPkg/DynamicCommand/DpDynamicCommand/DpDynamicCommand.inf on your platform**

**Notes:**
If you want to filter some performance data, please check the configuration of PcdPerformanceLibraryPropertyMask in below PCD part for more information. 

# Performance Dump Tool
* **UEFI Shell tool: DP**<BR>
   **(1) Use DP command:**<BR>
       You can build ShellPkg/DynamicCommand/DpDynamicCommand/DpDynamicCommand.inf on your platform,<BR>
       then you can input DP command in shell directly to get the performance data.

   **(2) Use DP application:**<BR>
       You can build ShellPkg/DynamicCommand/DpDynamicCommand/DpApp.inf,<BR>
       then you can use DP Application to dump performance data.

    DP command and application read the installed FPDT ACPI table itself to get the performance data.
# Performance modules 

## Performance library instances
### 1. edk2/MdeModulePkg/Library/PeiPerformanceLib
   This library instance provides infrastructure for PEIMs(including PeiCore) to log performance data.<BR>
   It will create FPDT record to save the performance data and pass the FPDT records to DxeCorePerformanceLib through 
   Guided Hob.

### 2. edk2/MdeModulePkg/Library/SmmPerformanceLib
   This library instance provides infrastructure for SMM drivers to log performance data.<BR>
   It consumes SMM PerformanceMeasurement Protocol published by SmmCorePerformanceLib to log performance data.

### 3. edk2/MdeModulePkg/Library/SmmCorePerformanceLib
   This library instance is used by SmmCore. It will:
   * Cache the performance data in SmmCore.
   * Publish SMM PerformanceMeasurement protocol consumed by SmmPerformanceLib to log the performance data in SMM.
   * Report the performance data in SMM phase (FPDT records) to the FirmwarePerformanceDataTableSmm driver.

### 4. edk2/MdeModulePkg/Library/DxePerformanceLib
   This library instance provides infrastructure for DXE drivers to log performance.<BR>
   It consumes PerformanceMeasurement Protocol published by DxeCorePerformanceLib to log performance data.

### 5. edk2/MdeModulePkg/Library/DxeCorePerformanceLib
   This library instance is used by DxeCore. It will:<BR>
   * Cache the performance data in DxeCore.
   * Cache the performance data from PEI phase in Guided HOB passed by PeiPerformanceLib
   * Publish the PerformanceMeasurement protocol consumed by DxePerformanceLib to log the performance data in DXE phase.
   * Communicates with FirmwarePerformanceDataTableSmm driver to get the performance data in SMM phase.
   * Report all the performance data(FPDT records) to the FirmwarePerformanceDataTableDxe driver.

## Performance drivers
### 1. edk2/MdeModulePkg/Universal/Acpi/FirmwarePerformanceDataTablePei
   This modules collects performance data for:
   * S3 Resume Performance Record:<BR>
     S3ResumePerformanceRecord.**ResumeCount**<BR>
     S3ResumePerformanceRecord.**FullResume**<BR>
     S3ResumePerformanceRecord.**AverageResume**<BR>

     S3 resume module reports status code of EFI_SW_PEI_PC_OS_WAKE.<BR>
     FirmwarePerformanceDataTablePei module listens to the status code and save the value of<BR>
     ResumeCount, FullResume and AverageResume.
   * Boot performance records on S3 boot path reported by PeiPerformanceLib through Guided Hob.

   And it will update the ACPI FPDT with new collected data.<BR>
   **This module is used in S3 boot.**

### 2. edk2/MdeModulePkg/Universal/Acpi/FirmwarePerformanceDataTableSmm
   This module collects performance data for:
   * S3 Suspend Performance Record:<BR>
     S3SuspendPerformanceRecord.**SuspendStart**<BR>
     S3SuspendPerformanceRecord.**SuspendEnd**<BR>

     Platform/chipset codes report SuspendStart status code when S3 state is triggered,
     report SuspendEnd status code when go to sleep.<BR>
     FirmwarePerformanceDataTableSmm listens to the status code to save the time data.<BR>
     `Progress Code for S3 Suspend start:`<BR>
     `PROGRESS_CODE_S3_SUSPEND_START = (EFI_SOFTWARE_SMM_DRIVER | (EFI_OEM_SPECIFIC | 0x00000000))    = 0x03078000 `
     `gEfiMdeModulePkgTokenSpaceGuid.PcdProgressCodeS3SuspendStart|0x03078000|UINT32|0x30001032  `

     `Progress Code for S3 Suspend end: `<BR>
     `PROGRESS_CODE_S3_SUSPEND_END   = (EFI_SOFTWARE_SMM_DRIVER | (EFI_OEM_SPECIFIC | 0x00000001))    = 0x03078001  `
     `gEfiMdeModulePkgTokenSpaceGuid.PcdProgressCodeS3SuspendEnd|0x03078001|UINT32|0x30001033  `

   * Boot performance records in SMM phase reported by SmmCorePerformanceLib.

   It will pass the boot performance records in SMM phase to DxeCorePerformanceLib through SMM communocation.
   **This module is used in normal boot and S3 boot.**

### 3. edk2/MdeModulePkg/Universal/Acpi/FirmwarePerformanceDataTableDxe
   This module collects performance data for:
   * Firmware Basic Boot Performance Record:<BR>
     BasicBootPerformanceRecord.**ResetEnd**<BR>
     Platform SEC library caches the time stamp of ResetEnd through SecPerformancePpi.<BR>
     And SecCore passes the SecPerformancePpi to PeiCore as notification type and add SecPerformancePpiCallback function to get SEC performance data and build Guided HOB to convey the performance data to DXE phase.<BR> 
     So in PEI phase, SecPerformancePpiCallback is called, and then ResetEnd data can be got in DXE phase through Guided HOB.  
     Currently FirmwarePerformanceDataTableDxe gets ResetEnd data from the Guided HOB.

     BasicBootPerformanceRecord.**OsLoaderLoadImageStart**<BR>
     BasicBootPerformanceRecord.**OsLoaderStartImageStart**<BR>
     Core codes (UefiBootManagerLib) report the status code.<BR>
     FirmwarePerformanceDataTableDxe listens to the status code to get and save the time data.<BR>
     `Progress Code for OS Loader LoadImage start.`<BR>
     `PROGRESS_CODE_OS_LOADER_LOAD   = (EFI_SOFTWARE_DXE_BS_DRIVER | (EFI_OEM_SPECIFIC | 0x00000000)) = 0x03058000`<BR>
     `gEfiMdeModulePkgTokenSpaceGuid.PcdProgressCodeOsLoaderLoad|0x03058000|UINT32|0x30001030 `<BR>

     `Progress Code for OS Loader StartImage start.`<BR>
     `PROGRESS_CODE_OS_LOADER_START  = (EFI_SOFTWARE_DXE_BS_DRIVER | (EFI_OEM_SPECIFIC | 0x00000001)) = 0x03058001`<BR>
     `gEfiMdeModulePkgTokenSpaceGuid.PcdProgressCodeOsLoaderStart|0x03058001|UINT32|0x30001031`<BR>

     BasicBootPerformanceRecord.**ExitBootServicesEntry**<BR>
     FirmwarePerformanceDataTableDxe registers callback function when ExitBootServices Event is signaled and save the time data.

     BasicBootPerformanceRecord.**ExitBootServicesExit**<BR>
FirmwarePerformanceDataTableDxe listens to the status code of EFI_SW_BS_PC_EXIT_BOOT_SERVICES to save the time data.<BR>

   * All boot performance records reported by DxeCorePerformanceLib.

   And it will install FPDT to ACPI table.<BR>
   **This module is used in normal boot.**

# PCD

### PcdPerformanceLibraryPropertyMask
  User can configure it to enable the performance infrastructure and also can filter some uninterested performance data.<BR>
  The meaning of each bits in PcdPerformanceLibraryPropertyMask as below:
  * BIT0 - Enable Performance Measurement.
  * BIT1 - Disable Start Image Logging.
  * BIT2 - Disable Load Image logging.
  * BIT3 - Disable Binding Support logging.
  * BIT4 - Disable Binding Start logging.
  * BIT5 - Disable Binding Stop logging.
  * BIT6 - Disable all other general Perfs.
  * BIT1-BIT6 are evaluated when BIT0 is set.

  So **PcdPerformanceLibraryPropertyMask=0:** disable performance infrastructure.<BR>
     **PcdPerformanceLibraryPropertyMask=1:** enable performance infrastructure.<BR>
     **PcdPerformanceLibraryPropertyMask=3:** filter the performance data of Start Image behavior.<BR>
     ......

### PcdEdkiiFpdtStringRecordEnableOnly
  Control the FPDT record format used to store the performance entry when performance enable.
  **PcdEdkiiFpdtStringRecordEnableOnly=TRUE:** FPDT_DYNAMIC_STRING_EVENT_RECORD will be used to store every performance Entry.<BR>
  **PcdEdkiiFpdtStringRecordEnableOnly=FALSE:** different FPDT records will be used to store different performance entries.

  **Notes:** The configuration of this PCD may impact the tool to parse the ACPI FPDT to dump the performance data.<BR>
  Current DP tool in ShellPkg can dump the performance data regardless of the PcdEdkiiFpdtStringRecordEnableOnly setting
