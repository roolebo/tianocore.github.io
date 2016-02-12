First check out [[Getting Started with EDK II]] for downloading the
latest EDK II development project with your build environment.

Are you new to using git? If so, then the [[New to git]] page may be
helpful.

The developer process for the EDK II project
--------------------------------------------

1.  Setup the EDK II tree if you do not have one

    * This is document on the [[SourceForge to Github Quick Start]] page

2.  Create and checkout a topic branch for new feature or bug fix

    `$ git checkout -b <new-dev-branch> origin/master`

3.  Make changes in the working tree

4.  Break up working tree changes into independent commits that do not
    break *git bisect*
    -   [Commit-Partitioning](Commit-Partitioning "wikilink")

    -  To stage all modifications: `$ git add -u`

    -  To add new files: `$ git add <path-to-new-file>`

    -  To have git prompt you to selectively stage changes: `$ git add -p`

5.  Follow the commit message template given below when writing commit
    messages

    - [Commit-Message-Format](Commit-Message-Format "wikilink")

    - To commit staged changes: `$ git commit`

      - Add the `-s` parameter to automatically append your
        Signed-off-by tag to the commit message.

6.  Use the ‘PatchCheck.py’ script under ‘edk2\\BaseTools\\Scripts’
    directory to verify the commits are correctly formatted

    - To check the latest <N> changes: `$ python BaseTools/Scripts/PatchCheck.py -<N>`

      - For example, 2 changes would be: `$ python BaseTools/Scripts/PatchCheck.py -2`

    - It is strongly recommended that you run PatchCheck.py after each
      commit. You can then easily amend the commit to correct any
      issues.

7.  Get the latest changes from origin

    `$ git fetch origin`

    Note: This updates origin/master, but not your local master
    branch. (origin/master may have newer commits than master.)

8.  Rebase the topic branch onto master branch

    `$ git rebase origin/master`

9. Create patch (serial) to the [[edk2-devel]] mailing list

    - Clean out any old patches: `$ rm *.patch`

    - Generate new patch files: `$ git format-patch -M --thread origin/master`

      - Add the `--cover-letter` parameter for long patch series. (Be
        sure to edit the cover-letter.)

      - Add the `--subject-prefix="PATCH v2"` if you are sending out a
        second version of the patch series.

    - `$ git send-email *.patch`

10. Modify local commits based on the review feedbacks and repeat steps
    3 to 9

    - For the latest commit, you can use `$ git commit --amend`

    - For multiple commits use `$ git rebase -i origin/master`

    - Consult your git gurus on edk2-devel or irc channel if you have
      questions.

The maintainer process for the EDK II project
---------------------------------------------

1.  Determine if a patch has met the review requirements for the package

2.  Update the origin/master from the server

    `$ git fetch origin`

3.  Create and checkout an integration branch

    `$ git checkout -b <new-integration-branch> origin/master`

4.  Add commits on the integration branch

    `$ git am <patch-file>`

5.  Rebase commit message to include any reviewed-by or other
    attributions

    `$ git rebase -i origin/master`

    - Edit lines to have an 'r' to 'reword' the commit. This will
      allow you to add the Reviewed-by attributions.

6.  Push changes to the EDK II project repository
    -   Pushing the integration branch directly to origin/master is
        preferred

    `$ git push origin HEAD:master`

    - Using the `--dry-run` parameter will show exactly what is going
      to be updated. Use this if you are paranoid. :)

Updating your master branch
---------------------------

1.  Get the latest changes from origin

    `$ git fetch origin`

2.  Change to the local master branch

    `$ git checkout master`

3.  Apply the latest server changes to you local master branch.

    `$ git rebase origin/master`

**See Also**
------------

-   [[Commit-Message-Format]]
-   [[Code-Style]]
