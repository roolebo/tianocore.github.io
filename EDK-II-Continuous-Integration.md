# EDK II Continuous Integration Proposal

# Phase 1 (edk2 repository only)
* Remove write access to edk2 repo
* EDK II Maintainers use GutHub PR instead of push
* Only accept PR from EDK II Maintainers.  Reject all other PRs.
* Run basic Pre-commit checks
* If all Pre-commit checks pass, then auto commit changes
* If any Pre-commit check fails, then notify submitter
* Limit pre-commit check execution time to 10 minutes
* Provide on-demand builds to EDK II Maintainers

## Proposed Pre-Commit Checks in Phase 1
* PatchCheck.py

## Proposed Pre-Commit Checks in Phase 2
* Verify Reviewed-by and Acked-by tags are present with correct maintainers
* Verify no non-ASCII characters in modified files
* Verify no binary files in set of modified files
* Verify Package Dependency rules in modified files

## Proposed Pre-Commit Checks in Phase 3
* Run ECC on modified files
* Verify modified modules/libs build
* Run available Host Based tests against modified modules/libs

# Post Commit Checks
* Build all modules/libs/platforms that consume modified content

# Daily Builds
* Build critical packages
* Build critical platforms
* Verify that Doxygen Documentation can be generated

# Weekly Builds
* Build all packages
* Build all platforms
* Publish Doxygen Documentation to a web site

# Release Builds
* Same as weekly builds
* Full regression testing
* Publishes binary files to release pages

# Possible Continuous Integration Actions
* PatchCheck.py
* Verify Reviewed-by and Acked-by tags are maintainers
* Verify no non-ASCII characters in modified source files
* Verify no binary files in set of modified files
* Verify Package Dependency rules in modified files
* Verify modified modules/libs build
* Run Host Based tests against modified modules/libs
* cppcheck
* ECC (EFI Code Checker)
  + Difficult to address all warnings/issues/false positives reported
  + May need to maintain an exception list
* Static Analysis against modified modules/libs
  + CLANG static analysis
  + Coverity: https://scan.coverity.com/
  + Difficult to address all warnings/issues/false positives reported
  + May need to maintain an exception list
* pyflakes (for python sources)
* pylama (for python sources)
* Generate Package Documentation (Doxygen based)
* Build all Packages/Platforms for all supported CPU architectures
  (IA32, X64, ARM, AARCH64, EBC), all supported tool chains (VS2015, VS2017,
  GCC5, XCODE5), and all supported build targets (DEBUG, RELEASE, NOOPT).
  Needs further discussion on required coverage.
* Boot platforms to UEFI Shell
* Run UEFI SCTs and collect results for platforms
* Linaro LAVA CI
  + https://validation.linaro.org/static/docs/v2
  + https://validation.linaro.org/static/docs/v2/lava_ci.html#continuous-integration
  + https://lkft.linaro.org
* Boot platforms to OS(s)
* Integration/Regression tests that require full OS boots
* Build binary releases of components (e.g. UEFI Shell, OVMF)

# Possible Platform Testing Actions
* OVMF
  + IA32 DXE boot tests
  + X64 DXE boot tests
  + ArmVirt QEMU boot tests
  + S3 enabled tests
  + SMM_REQUIRE enabled tests
  + Boot using both KVM and QEMU environments
  + HTTPS Boot Tests
  + UEFI Secure Boot Sanity Testing
    - Enroll UEFI Secure Boot Keys
    - UEFI Secure Boot testing of "unsigned" images
    - UEFI Secure Boot testing of "signed but not recognized" images
    - UEFI Secure Boot testing of "signed and accepted" images
    - UEFI Secure Boot testing of "signed but blacklisted" images
  + Project that offers a python script that automates communicating with the
    UEFI shell over the emulated serial port. Used in downstream package builds,
    for packaging a pre-enrolled variable store template file.
    - https://github.com/puiterwijk/qemu-ovmf-secureboot

# Evaluations and Supporting Background Materials
* Background
  + https://en.wikipedia.org/wiki/Continuous_integration
* GitHub Continuous Integration services
  + https://github.com/marketplace/category/continuous-integration
* Jenkins Evaluation for booting and running tests in physical hardware
  + Contacts
    - Rebecca Cran <rebecca@bsdio.com>
  + https://ci.bsdio.com/job/edk2-master/
  + https://ci.bsdio.com/blue/organizations/jenkins/edk2-ci/activity
  + https://ci.bsdio.com/blue/organizations/jenkins/edk2-ci/detail/edk2-ci/2/pipeline
* GitLab Evaluation
  + Contacts
    - Laszlo Ersek <lersek@redhat.com>
    - Philippe Mathieu-Daudé <philmd@redhat.com>
  + https://gitlab.com/philmd/edk2/pipelines
* Azure Pipeline Evaluation for CPU Archs, tool chain tags, and build targets
  + Contacts
    - Michael D Kinney <michael.d.kinney@intel.com>
  + https://github.com/mdkinney/edk2-ci
  + https://dev.azure.com/mikekinney/edk2-ci/_build?definitionId=1
* Azure Pipelines Evaluation with HBFA integration
  + Contacts
    - Sean Brogan <sean.brogan@microsoft.com>
    - Bret Barkelew <Bret.Barkelew@microsoft.com>
  + https://github.com/spbrogan/edk2-staging/tree/edk2-stuart-ci-latest
  + To work with this branch and run tests immediately, all you need to do is:
```
pip install --upgrade -r requirements.txt
stuart_setup -c .\CISettings.py
stuart_update -c .\CISettings.py
stuart_ci_build -c .\CISettings.py --Tool_Chain VS2017
```
  + Branch is monitored for CI and PR gating in the following Azure build pipeline.
    - https://dev.azure.com/tianocore/edk2-ci-play/_build?definitionId=12
  + Results show that this CI process is running build CI and DSC checking and
    automatically running a host-based unit test and all of the results are
    visible in a single view.
    - https://dev.azure.com/tianocore/edk2-ci-play/_build/results?buildId=216&view=ms.vss-test-web.build-test-results-tab
    - https://dev.azure.com/tianocore/edk2-ci-play/_build/results?buildId=215&view=ms.vss-test-web.build-test-results-tab
  + Depends on pip installable tool from the following TianoCore repos
    - https://github.com/tianocore/edk2-pytool-extensions
    - https://github.com/tianocore/edk2-pytool-library
  + Documentation for edk2-pytools
    - https://github.com/tianocore/edk2-pytool-library/tree/master/docs
    - https://github.com/tianocore/edk2-pytool-extensions/tree/master/docs
