Below is the directory tree of IntelFrameworkModulePkg.
The number in [] after the directory name is the accordingly bugzilla number tracking the removal of it.

IntelFrameworkModulePkg
	+---Bus
	|   +---Isa [1315]
	|   \---Pci 
	|       +---IdeBusDxe [1339]
	|       \---VgaMiniPortDxe [1316]
	+---Csm
	|   +---BiosThunk
	|   |   +---BlockIoDxe
	|   |   +---KeyboardDxe
	|   |   +---Snp16Dxe
	|   |   \---VideoDxe
	|   \---LegacyBiosDxe
	+---Include
	|   +---Guid
	|   +---Library
	|   \---Protocol
	+---Library
	|   +---BaseUefiTianoCustomDecompressLib
	|   +---DxeCapsuleLib[1342]
	|   +---DxeReportStatusCodeLibFramework [1318]
	|   +---GenericBdsLib [1314]
	|   +---LegacyBootMaintUiLib
	|   +---LegacyBootManagerLib
	|   +---LzmaCustomDecompressLib [1340]
	|   +---PeiDxeDebugLibReportStatusCode [1318]
	|   +---PeiRecoveryLib [1299]
	|   +---PeiS3Lib [1299]
	|   +---PlatformBdsLibNull [1314]
	|   \---SmmRuntimeDxeReportStatusCodeLibFramework [1318]
	\---Universal
	    +---Acpi
	    |   +---AcpiS3SaveDxe
	    |   \---AcpiSupportDxe
	    +---BdsDxe [1314]
	    +---Console\VgaClassDxe[1316]
	    +---CpuIoDxe [1344]
	    +---DataHubDxe [1318]
	    +---DataHubStdErrDxe [1318]
	    +---FirmwareVolume [1346]
	    |   +---FwVolDxe
	    |   \---UpdateDriverDxe
	    +---LegacyRegionDxe[1345]
	    +---SectionExtractionDxe [1347]
	    \---StatusCode [1318]
	        +---DatahubStatusCodeHandlerDxe
	        +---Pei
	        \---RuntimeDxe
	
