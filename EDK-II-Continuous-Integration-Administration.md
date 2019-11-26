# EDK II Continuous Integration Administration

## Configuring edk2 repository

1) Azure Pipelines Configuration Steps Part I
   1) Goto https://dev.azure.com/tianocore
   2) Create new project in TianoCore org called `edk2-ci`
   3) Disable Boards
   4) Disable Repositories

2) GitHub Configuration Steps Part I
   1) Goto https://github.com/tianocore
   2) Select Settings
   3) Select Installed GitHub Apps
   4) Select Azure Pipelines -> Configure
      * Enable `tianocore/edk2` repository
      * Redirects to Azure Pipelines login.  Select TianoCore org and
        `edk2-ci` project to complete link and authentication between Azure
        Pipelines TianoCore organization and GitHub TianoCore organization.

3) Azure Pipelines Configuration Steps Part II
   1) Goto https://dev.azure.com/tianocore/edk2-ci/_build
   2) Create New Pipeline called `Ubuntu GCC5 CI` for post commit checks
      * YAML: Select file https://github.com/tianocore/edk2/blob/master/.azurepipelines/Ubuntu-GCC5.yml
      * Name: Set to `Ubuntu GCC5 CI`
      * Triggers:
        * CI - No changes
        * PR - Override + Disable
      * Variables: No changes
      * Save pipeline changes
      * Run pipeline and verify all checks pass
   3) Create New Pipeline called `Windows VS2019 CI` for post commit checks
      * YAML: Select file https://github.com/tianocore/edk2/blob/master/.azurepipelines/Windows-VS2019.yml
      * Name: `Windows VS2019 CI`
      * Triggers
        * CI - No changes
        * PR - Override + Disable
      * Variables: No changes
      * Save pipeline changes
      * Run pipeline and verify all checks pass
   4) Create New Pipeline called `Ubuntu GCC5 PR` for pre commit checks
      * YAML: Select file https://github.com/tianocore/edk2/blob/master/.azurepipelines/Ubuntu-GCC5.yml
      * Name: `Ubuntu GCC5 PR`
      * Triggers
        * CI - Override + Disable
        * PR - No changes
      * Variables: No changes
      * Save pipeline changes
      * Run pipeline.  Pipeline will fail because it requires a GitHub PR.  Must
        run for pipeline name to show up in GitHub Branch Protection Rules.
   5) Create New Pipeline called `Windows VS2019 PR` for pre commit checks
      * YAML: Select file https://github.com/tianocore/edk2/blob/master/.azurepipelines/Windows-VS2019.yml
      * Name: `Windows VS2019 PR`
      * Triggers
        * CI - Override + Disable
        * PR - No changes
      * Variables: No changes
      * Save pipeline changes
      * Run pipeline.  Pipeline will fail because it requires a GitHub PR.  Must
        run for pipeline name to show up in GitHub Branch Protection Rules.
   6) Add PatchCheck pipeline for pre commit checks
      * YAML: Select file https://github.com/tianocore/edk2/blob/master/.azurepipelines/Ubuntu-PatchCheck.yml
      * Name: `tianocore.PatchCheck`
      * Triggers
        * CI - Override + Disable
        * PR - No changes
      * Variables: No changes
      * Save pipeline changes
      * Run pipeline.  Pipeline will fail because it requires a GitHub PR.  Must
        run for pipeline name to show up in GitHub Branch Protection Rules.

4) GitHub Configuration Steps Part II
   1) Goto https://github.com/tianocore/edk2
   2) Select Settings
   3) Select Branches
   4) Select Branch Protection Rules
   5) Select `master` -> Edit
      * Enable `Require status checks to pass before merging`
      * Enable `Require branches to be up to date before merging`
      * Enable `Windows VS2019 PR` check
      * Enable `Ubuntu GCC5 PR` check
      * Enable `tianocore.PatchCheck` check
   6) Goto https://github.com/tianocore
   7) Select Settings
   8) Select Installed GitHub Apps
   9) Select Mergify -> Configure
      * Enable tianocore/edk2 repo

5) Update Status Badge Links
   1) Goto https://dev.azure.com/tianocore/edk2-ci/_build
   2) Select `Windows VS2019 CI`
      * Use '...' menu in upper right and select Status badge
      * Copy Sample markdown
      * Update links in Build Status section of https://github.com/tianocore/edk2/blob/master/Readme.md
   3) Select `Ubuntu GCC5 CI`
      * Use '...' menu in upper right and select Status badge
      * Copy Sample markdown
      * Update links in Build Status section of https://github.com/tianocore/edk2/blob/master/Readme.md
   4) Submit changes to Readme.md for EDK II code review and submit GitHub PR to
      test personal build and Mergify commit once review passes.
