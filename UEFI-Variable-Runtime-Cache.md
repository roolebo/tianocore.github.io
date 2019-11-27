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
The default PCD value is TRUE. The feature can be disabled by simply setting the PCD value to "FALSE" in a platform
DSC file.

# Problem Statement
The UEFI Runtime Service GetVariable () is called very often throughout the boot including OS runtime. Each Runtime
Service GetVariable () call triggers an SMI which negatively impacts system performance.

## Issues with SMM
From a system functionality standpoint, SMM is typically undesirable for the following reasons:
1. Core rendezvous: When the system enters SMM, all CPU activity is blocked at other privilege levels.
2. Interrupt latency: Real-Time Operating Systems (RTOS) have stringent requirements to service system interrupts
   which is severely impacted by frequent SMIs at OS runtime.

## Platforms Have Little Choice
Today's computer systems must comply with a myriad of industry specifications that often leverage the UEFI variable
mechanism as defined in the UEFI specification as a form of OS independent non-volatile storage. Sometimes other system
software interacts with UEFI variables outside the control of other software impacted. The following examples are
intended to help illustrate this statement.
1. Firmware interfaces such as UEFI capsule update requires UEFI variables.
2. Industry specifications define variables such as BootOrder, OsIndications, and UEFI Secure Boot related variables
   such as pk, kek, db, dbx, etc. creating an interface between the platform firmware and operating system.
3. Operating systems such as Microsoft Windows 10 sometimes issue periodic UEFI variable reads independent of user
   software.
4. Operating systems such as Microsoft Windows 10 uses UEFI variables to manage checkpoints of disk dumps during bug
   check scenarios.

In any case, reducing the overall system impact due to UEFI variables benefits all software on the system.

## Sample GetVariable () Impact
The following data is intended to show why this feature can be useful. Assume an RTOS has a maximum latency allowance
of 10us. The following measurements were taken from an Intel Apollo Lake Reference and Validation Platform and show this
threshold is not achievable with any SMM usage.

Observation                                                  | Duration
-------------------------------------------------------------|------------------
Pure SMI entry latency (RSM w/ no rendezvous)                | 40us
Dummy SMI handler (port 0xB2 I/O port w/ 0x88)               | 180us
RT->GetVariable () (called for an existing UEFI variable)    | 220us
RT->GetVariable () (called for a non-existing UEFI variable) | 272us

> Note: This data does not reflect the performance of the product in any particular configuration and is only provided
for illustrative purposes of the relative duration for the given scenarios.

## A Phased Approach
Ideally, SMM could be eliminated entirely. However, SMM provides a ubiquitous isolated execution environment to
authenticate UEFI variable requests. Further, SMM provides a trusted software environment to manage UEFI variable
transactions to non-volatile storage (e.g. SPI flash or eMMC/UFS RPMB) at OS runtime when hardware enforcement of
write access is restricted to SMM.

### Priority: Reduce SMM Usage for Getting UEFI Variables
The rationale for preserving SMM applies to SetVariable () but not GetVariable () or GetNextVariableName (). This
realization led to the UEFI Variable Runtime cache feature. Fortunately, getting variables is also the common case.

On an Intel&reg; Atom Reference and Validation Platform (RVP), it was found that after a first boot (also referred to
as a "manufacturing boot" in which many UEFI variables are written to initialize the UEFI variable store), the number
of GetVariable () calls exceeded 150 while the number of SetVariable () calls was less than 10. GetVariableName () was
also invoked multiple times which is often called many times iterating over the current set of variables each time
invoking an SMI.

On the same Intel&reg; Apollo Lake system used in the earlier data table, it was found that with the UEFI Variable
Runtime Cache feature enabled, the total GetVariable () time for an existing UEFI variable decreased to 5us (from 220us).

Therefore, analysis shows eliminating SMIs on Runtime Service GetVariable () and GetNextVariableName () calls is
possible and can lead to the greatest potential improvement in terms of SMM reduction across the UEFI variable services.
