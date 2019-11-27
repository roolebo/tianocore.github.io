The UEFI Variable Runtime Cache feature was introduced to reduce the total number of SMIs triggered and therefore total
system time in SMM when SMM UEFI variables are enabled.

* Pros:
  * Improved system boot time
  * Improved system stability
  * Less SMI impact on the operating system (of particular importance in real-time operating systems)
* Cons:
  * Increase in runtime system memory

    > Note: The memory increase is relatively minimal where the increase size is equal to the sum of the size of all
            non-volatile UEFI variable stores on the platform. Typically there is just one non-volatile variable store.

The feature is enabled and disabled by the following FeaturePCD in MdeModulePkg:
```
  ## Indicates if the UEFI variable runtime cache should be enabled.
  #  This setting only applies if SMM variables are enabled. When enabled, all variable
  #  data for Runtime Service GetVariable () and GetNextVariableName () calls is retrieved
  #  from a runtime data buffer referred to as the "runtime cache". An SMI is not triggered
  #  at all for these requests. Variables writes still trigger an SMI. This can greatly
  #  reduce overall system SMM usage as most boots tend to issue far more variable reads
  #  than writes.<BR><BR>
  #   TRUE  - The UEFI variable runtime cache is enabled.<BR>
  #   FALSE - The UEFI variable runtime cache is disabled.<BR>
  # @Prompt Enable the UEFI variable runtime cache.
  gEfiMdeModulePkgTokenSpaceGuid.PcdEnableVariableRuntimeCache|TRUE|BOOLEAN|0x00010039
```
The default PCD value is TRUE. The feature can be disabled by simply setting the PCD value to "FALSE" in a platform DSC file.
