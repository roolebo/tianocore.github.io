EDK II Development Process
==========================

First check out [[Getting Started with EDK II]] for downloading the latest EDK II development project with your build environment.

**Development Process for the EDK II Project**
----------------------------------------------

The developer process for the EDK II project is:

1.  Setup the EDK II tree if you do not have one
2.  Create and checkout a topic branch for new feature or bug fix
3.  Make changes in the working tree
4.  Break up working tree changes into independent commits that do not
    break *git bisect*
    -   [Commit-Partitioning](Commit-Partitioning "wikilink")
5.  Follow the commit message template given below when writing commit
    messages
    -   [Commit-Message-Format](Commit-Message-Format "wikilink")
6.  Use the ‘PatchCheck.py’ script under ‘edk2\\BaseTools\\Scripts’
    directory to verify the commits are correctly formatted
7.  Update the master branch (pull or fetch/merge)
8.  Rebase the topic branch onto master branch
9. Create patch (serial) to the [[edk2-devel]] mailing list or upload
   the topic branch to your forked EDK II project and send the URL
   and branch name of the fork to the above mailing list
    -   Using *git send-email* is the preferred method for posting
        patches to the mailing list
10. Modify local commits based on the review feedbacks and repeat steps
    3 to 9

The maintainer process for the EDK II project is:

1.  Determine if a patch has met the review requirements for the package
2.  Update the master branch
3.  Create and checkout an integration branch
4.  Integrate reviewed commits on the integration branch
5.  Rebase commit message to include any reviewed-by or other
    attributions
6.  Push changes to the EDKII project repository
    -   Pushing the integration branch directly to origin/master is
        preferred

**See Also**
------------

-   [[Commit-Message-Format]]
-   [[Code-Style]]
