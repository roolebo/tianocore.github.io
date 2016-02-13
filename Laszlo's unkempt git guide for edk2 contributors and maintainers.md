Disclaimer
==========

This is a quick and dirty, simplified and slightly modified description
of my own edk2 workflow with git. It expressly defers to the [EDK II
Development Process
article](https://github.com/tianocore/tianocore.github.io/wiki/EDK-II-Development-Process)
on the TianoCore wiki. It doesn't try to be generic, focuses only on
GNU/Linux (that's what I use). It will not go into many details about
git; if you are interested, you'll have to research those concepts on
the web yourself.

Also, this is very specific to edk2. Other projects have different
workflows.

Contributor workflow
====================

1.   Create an account on GitHub.

2.   Enable SSH authentication for your account.

     https://help.github.com/articles/generating-an-ssh-key/

     When completing this step, you should end up with a new keypair
     under ~/.ssh/, for example:

       id_rsa_for_github
       id_rsa_for_github.pub

     and the following stanza in your ~/.ssh/config:

     ```
       Host github.com
         User          git
         IdentityFile  ~/.ssh/id_rsa_for_github
     ```

3.   Fork the following repository on GitHub into your own GitHub
     account, using the GitHub web GUI:

       https://github.com/tianocore/edk2/

     (My personal fork is at https://github.com/lersek/edk2/)

4.   Clone the official edk2 repository to your local computer:

     ```
       cd some-appropriate-directory
       git clone https://github.com/tianocore/edk2.git
     ```

5.   Implement the following git settings for your local clone, i.e.,
     while standing in your local edk2 directory (these steps don't need
     customization):

     ```
       git config am.keepcr              true
       git config am.signoff             true
       git config cherry-pick.signoff    true
       git config color.diff             true
       git config color.grep             auto
       git config commit.signoff         true
       git config core.abbrev            12
       git config core.pager             cat
       git config core.whitespace        cr-at-eol
       git config diff.algorithm         patience
       git config diff.ini.xfuncname     '^\[[A-Za-z0-9_.,   ]+]'
       git config diff.renames           copies
       git config format.signoff         false
       git config notes.rewriteRef       refs/notes/commits
       git config sendemail.chainreplyto false
       git config sendemail.thread       true
     ```

6.   Also implement the following -- they need customization:

     ```
       git config sendemail.smtpserver FQDN_OF_YOUR_LOCAL_SMTP_SERVER
       git config user.email           "Your Email Address"
       git config user.name            "Your Name"
     ```

7.   Create a file called "tianocore.template" somewhere outside your
     edk2 clone, with the following contents (remove leading
     whitespace). Note that the last line requires customization.

     ```
       [empty line]
       [empty line]
       Contributed-under: TianoCore Contribution Agreement 1.0
       Signed-off-by: Your Name <Your Email Address>
     ```

8.   Standing in your edk2 clone, implement the following setting
     (requires customization):

     ```
       git config commit.template \
         FULL_PATHNAME_OF_FILE_CREATED_IN_LAST_STEP
     ```

9.   Open the file

       .git/info/attributes

     (create it if it doesn't exist), and add the following contents
     (with the leading whitespace removed):

     ```
       *.efi     -diff
       *.EFI     -diff
       *.bin     -diff
       *.BIN     -diff
       *.raw     -diff
       *.RAW     -diff
       *.bmp     -diff
       *.BMP     -diff
       *.dec     diff=ini
       *.dsc     diff=ini
       *.dsc.inc diff=ini
       *.fdf     diff=ini
       *.fdf.inc diff=ini
       *.inf     diff=ini
     ```

10.  Create a file called "edk2.diff.order" somewhere outside your local
     clone, with the following contents (removing the leading
     whitespace):

     ```
       *.dec
       *.dsc.inc
       *.dsc
       *.fdf
       *.inf
       *.h
       *.vfr
       *.c
     ```

11.  Add your own fork of edk2 that lives on GitHub as a *remote* to
     your local clone:

     ```
       git remote add -f --no-tags \
         YOUR_GITHUB_ID \
         git@github.com:YOUR_GITHUB_ID/edk2.git
     ```

12.  At this point you are ready to start developing. Refresh your local
     master branch from the upstream master branch:

     ```
       git checkout master
       git pull
     ```

     The first command is extremely important. You should only run "git
     pull" while you are standing on your local master branch that
     tracks (and never diverges from) the upstream master branch.

     These commands will fetch any new commits from upstream master, and
     fast-forward your local tracking branch to the new HEAD.

13.  Create and check out a topic branch for the feature or bugfix that
     you would like to work on. The topic branch name requires
     customization of course.

     ```
       git checkout -b implement_foo_for_bar_v1 master
     ```

14.  Make sure you have the build environment set up:

     ```
       source edk2setup.sh
       make -C "$EDK_TOOLS_PATH"
     ```

15.  Implement the next atomic, logical step in your feature or bugfix.
     Test that it builds and works. You should not cross module (driver,
     library class, library instance) boundaries within a single patch,
     if possible.

16.  Add your changes gradually to the staging area of git (it is called
     the "index"):

     ```
       git add -p
     ```

     This command will ask you interactively about staging each separate
     hunk. See the manual for "git add".

17.  When done, you can run

     ```
       git status
     ```

     This will list the files with staged and unstaged changes. You can
     show the diff that is staged for commit:

     ```
       git diff --staged
     ```

     and also the diff that is not staged yet:

     ```
       git diff
     ```

18.  If you are happy with the staged changes, run:

     ```
       git commit
     ```

     This will commit the staged changes to your local branch called
     "implement_foo_for_bar_v1". You created this branch in step (13).

     Before the commit occurs, git will fire up your preferred editor
     (from the $EDITOR variable) for you to edit the commit message. The
     commit message will be primed from the template created in step
     (07) and configured in step (08).

     Above the template, you should add:

     - a subject line, no longer than 74-76 characters, consisting of

       PackageName: ModuleName: summary of changes

     - an empty line

     - one or more paragraphs that describe the changes in the patch. No
       line should be longer than 74 characters.

     - an empty line

     - One or more tags directly above the "Contributed-under" line
       (which comes from the template) that CC the package maintainers
       that are relevant for this specific patch. Consult the
       Maintainers.txt file. For example, if you wrote a patch for
       OvmfPkg, add:

     ```
       Cc: Jordan Justen <jordan.l.justen@intel.com>
       Cc: Laszlo Ersek <lersek@redhat.com>
     ```

19.  When you have committed the patch, it is best to confirm it adheres
     to the edk2 coding style. Run:

     ```
       python BaseTools/Scripts/PatchCheck.py -1
     ```

20.  If the command in step (19) reports problems, modify the source
     code accordingly, then go back to step (16) and continue from
     there. However, as a small but important change for step (18), run
     "git commit" with the --amend option:

     ```
       git commit --amend
     ```

     This will *squash* your fixups into the last commit, and it will
     also let you re-edit the commit message (the PatchCheck.py script
     can also find problems with the commit message format).

     Re-run step (19) as well, to see if your patch is now solid.

21.  Write your next patch. That is, repeat this procedure (goto (15))
     until you are happy with the series -- *each* single one of your
     patches builds and runs (implementing the next atomic, logical step
     in the bugfix or feature), and at the last patch, the feature /
     bugfix is complete.

22.  It is now time to publish your changes for review.

     (At this point, at the latest, it is important to review your full
     series using a git GUI; for example "gitk". Practically, any time
     you are in doubt, and especially before publishing patches, run
     "git status" and "gitk" (or another GUI tool) and go over your
     series.)

     First, we'll push your local branch to your personal repository on
     GitHub, under the same branch name.

     ```
       git push YOUR_GITHUB_ID master implement_foo_for_bar_v1
     ```

     This command will connect to github using your remote configuration
     added in step (11), employing the SSH authentication configured in
     step (02).

     It will update the master branch in your personal repo on GitHub to
     your local master branch (which in turn follows the official master
     branch, see step (12)).

     It will also push the topic branch you created under step (13).

23.  Now we'll format the patches as email messages, and send them to
     the list. Standing in the root of your edk2 directory, run the
     following (note that the "-O" option needs customization: please
     update the pathname to the file created in step (10)):

     ```
       rm -f -- *.patch

       git format-patch                               \
         --notes                                      \
         -O"/fully/qualified/path/to/edk2.diff.order" \
         --cover-letter                               \
         --numbered                                   \
         --subject-prefix="PATCH v1"                  \
         --stat=1000                                  \
         --stat-graph-width=20                        \
         master..implement_foo_for_bar_v1
     ```

     This command will generate an email file for each one of your
     commits. The patch files will be numbered. The file numbered 0000
     is a *cover letter*, which you should edit in-place:

     - in the "Subject:" line, summarize the goal of your series,

     - in the message body, describe the changes on a broad level,

     - *reference*, by complete URL, the "implement_foo_for_bar_v1"
       branch in your personal GitHub repo -- the one that you pushed in
       step (22),

     - finally, add *all* of the Cc: tags to the cover letter that you
       used across all of the patches. This will ensure that even if a
       maintainer is involved in reviewing one or two of your patches
       across the series, he or she will get a copy of your cover
       letter, which outlines the full feature or bugfix.

24.  Time to mail-bomb the list! Do the following:

     ```
     git send-email                 \
       --suppress-cc=author         \
       --suppress-cc=self           \
       --suppress-cc=cc             \
       --suppress-cc=sob            \
       --to=edk2-devel@lists.01.org \
       *.patch
     ```

     This command might ask you about sending the messages in response
     to another message (identified by Message-Id). Just press Enter
     without entering anything.

     You might want to run the command first with the --dry-run
     parameter prepended.

     The messages will be posted to the list only, and to the
     maintainers that you Cc'd explicitly in the commit messages.

     Once the messages are sent, you can remove the local patch files
     with:

     ```
       rm -f -- *.patch
     ```

25.  On the list, you will get feedback. In the optimal case, each patch
     will get a Reviewed-by tag (or an Acked-by tag) from at least one
     maintainer that is responsible for the package being touched by
     that patch. If you are lucky, you will also get Tested-by tags from
     some of them.

     Once all tags are in place, one of the maintainers will pick up the
     entire series, update the commit messages to include the above tags
     (Reviewed-by, Acked-by, Tested-by) at the bottom, and commit and
     push your patches to upstream master. If this happens, pop the
     champagne, and goto step (12).

26.  More frequently though, you will get requests for changes for
     *some* of your patches, while *others* of your patches will be
     fine, and garner Reviewed-by, Acked-by, and Tested-by tags. What
     you need to do in this case is:

     - create the next version of your local branch
     - pick up the tags that you got on the list
     - implement the requested changes
     - mark the v2 changes on each patch outside of the commit message
     - push the next version to your personal repo again
     - post the next version to the list

     In the following steps, we'll go through each of these in more
     detail.

27.  Create the next version of your local branch. Run the following
     commands in your edk2 tree:

     ```
       git checkout master
       git pull

       git checkout -b \
         implement_foo_for_bar_v2 \
         implement_foo_for_bar_v1

       git rebase master implement_foo_for_bar_v2
     ```

     These commands do the following: first they refresh (fast forward)
     your local master branch to the current upstream master.

     Then they fork version 2 of your topic branch off of version 1 of
     the same. Finally version 2 of the topic branch is rebased,
     non-interactively, on top of the refreshed master branch.

     The upshot is that at this point, you have an *identical* version
     of your series on top of *refreshed* master, under a name that
     states *v2*, without touching the original *v1* series.

     Theoretically the last command could run into conflicts, but those
     are unlikely for a low-churn project like edk2, and if you get
     those, you should ask for help on the list. Conflict resolution is
     outside of the scope of this writeup. For now we'll assume that
     your last command completes without errors.

28.  Pick up the tags that you got on the list. Run the following
     command:

     ```
       git rebase -i master implement_foo_for_bar_v2
     ```

     This will open your $EDITOR with a list of your patches, identified
     by commit hash and subject line, each prefixed with a rebase
     *action*. By default, the rebase action will be "pick".

     You should carefully go through the feedback you received on the
     list for the v1 posting. (An email client that supports threading
     is a hard requirement for this.) For each v1 patch where you
     received a tag (Reviewed-by, Tested-by, Acked-by), *replace* the
     "pick" action with "reword". Be sure not to modify anything else in
     the rebase action list.

     Once you modified these actions, save the file, and quit the
     editor. Git will now rebase your patches again (to the same
     refreshed local master branch), but now it will also stop at each
     patch that you marked "reword", and will let you edit the commit
     message for the patch. This is when you append the tags from the
     mailing list feedback to the very end of the commit message,
     underneath your own Signed-off-by tag. Save the updated commit
     message and quit the editor; git will continue the rebase.

     If you mess up a commit message, don't panic. There are two options
     to bail out. First, you can update the next commit message to an
     empty text file. Git rebase will then stop and expect you to issue
     further commands at the normal shell prompt. This is when you run

     ```
       git rebase --abort
     ```

     and everything will be exactly like at the end of step (27).

     The second option if you mess up a commit message (and you notice
     too late, i.e., the rebase finishes), is just to repeat step (28),
     and fix up the commit message.

     (There is a third option: the branch can be forcibly reset to a
     chronologically earlier HEAD, which you can collect from the
     reflog. But that is a very sharp tool and not recommended for now.)

     At the end of this step, you will have picked up the feedback tags
     from the list, for each affected patch individually.

29.  Implement the requested changes. For this you run again

     ```
       git rebase -i master implement_foo_for_bar_v2
     ```

     but this time, you replace the "pick" actions of the affected (= to
     be modified) patches with "edit". My *strong* recommendation is to
     set the "edit" action for exactly one patch in the series, and let
     the rest remain "pick". (There are cases when this is not the best
     advice, but once you get in those situations, you won't need this
     guide.)

     Okay then, git will start the rebase, and it will stop *right
     after* the patch you marked as "edit" is *committed*. Your working
     tree will be clean (no changes relative to the staging index), your
     staging index will also be clean (no changes staged relative to the
     last commit -- which is the patch you marked as "edit").

     At this point you modify the code as necessary, and build it and
     test it. Once satisfied, you run steps (16) and (17). After those
     steps, your working tree will be clean relative to the staging
     index, and the index will have all the necessary changes staged
     relative to the last commit (which you marked as "edit" in the
     rebase action list).

     Now, if you ran

     ```
       git commit
     ```

     at this point (i.e., step (18) verbatim), then git would *insert*
     the staged changes as a *separate patch* into your series, so
     *don't do that*; that's most likely not your intent. Instead, run

     ```
       git commit --amend
     ```

     which will squash your staged changes into the patch-to-be-edited.

     Then see steps (19) and (20).

     (I am leaving out some editing action types here, such as: dropping
     a patch entirely, inserting a new patch, reordering patches,
     squashing patches together, and especially splitting up patches
     into smaller patches, and moving hunks between patches. Those are
     completely doable, and constitute the absolute power that git has
     over subversion, but they are definitely beyond these basics.)

     Okay, your patch is fixed up now as the reviewer(s) requested; it
     builds, it runs (at the level expected at this stage into your
     series); PatchCheck.py is happy with it; and you have it committed.
     Time to run:

     ```
       git rebase --continue
     ```

     This will complete the rebase.

     You can repeat this step (step (29)) as many times as necessary.
     Again, I recommend to run a full rebase per each patch that needs
     an edit.

     A small caveat: if you significantly edit a patch, say, for the v3
     posting, for which you have received a Reviewed-by or Tested-by
     earlier, you are supposed to *drop* these tags, because your
     significant edits render them stale.

30.  Mark the v2 changes on each patch outside of the commit message.
     This step is not strictly required, but it is a *huge* help for
     reviewers and maintainers.

     Each time you finish a full rebase (an iteration of step (29)), you
     should run your git GUI ("gitk" or anything else), and locate the
     patch (by subject) that you just edited in step (29).

     Grab the SHA1 commit hash of that patch, and run:

     ```
       git notes edit COMMIT_HASH_OF_THAT_PATCH
     ```

     Git will again fire up your text editor, and allow you to attach
     *notes* to the commit. The distinction between a commit message and
     commit notes is that the notes are *ephemeral*. They will be
     included in a special section of the patch email, but they will
     never be included in the commit message itself, on the upstream
     master branch. They are perfect for communicating v1 -> v2 -> v3
     changes, per patch, during the evolution of a given patch series.

     So, please format the notes as follows:

       v2:
       - frobnicate quux [Jordan]
       - perturb xizzy [Laszlo]

     No line should be longer than 72 characters in the notes, and each
     entry should preferably mark who suggested that specific change for
     the patch.

     Save the notes file and quit your editor, git will apply the
     changes. If you need to reedit the note, just repeat this step
     (step (30)).

     Very importantly, every time you complete a rebase, your notes are
     *preserved*, even if you edit the patch itself (code or commit
     message) during the rebase. This is very important for v2 -> v3
     updates, because in that case you can add the v3 section *on top*
     of v2 in the notes!

31.  Push the next version to your personal repo again.

     Practically, repeat step (22), but using the branch name
     "implement_foo_for_bar_v2".

     (It is *very* important that you never ever modify
     "implement_foo_for_bar_v1" after you push it to your personal
     github repo. Namely, this FEATURE_BRANCH_vN kind of branch is
     supposed to reflect your vN mailing list posting precisely. Since
     your mailing list posting is read only (you cannot modify emails
     you sent), you must not modify the corresponding branches in your
     github repo either. If a new version is necessary, you'll post a
     new version, and you'll push a new branch too.)

32.  Post the next version to the list.

     In practice, repeat step (23), with the following modifications:

     - The subject prefix should state

       --subject-prefix="PATCH v2"

     - The commit range given should be

       master..implement_foo_for_bar_v2

     - The cover letter should reference the v2 branch pushed in step
       (31).

     - The cover letter should include or reference (with an URL to the
       mailing list archive) the cover letter of the v1 posting, and
       also summarize the v1->v2 changes.

     Then repeat step (24).

This is it, more or less, for a contributor. Nonetheless, I recommend
reading the rest even to contributors, because it will help them
understand how maintainers are supposed to operate, and how their
actions above assist maintainers in doing their work.

Maintainer workflow
===================

1.   You need the same settings in your edk2 clone as a contributor.
     This includes contributor steps (01) through (11).

2.   You get patches to review, either by CC, or you notice them on the
     list.

     If you can immediately point out problems with (some of) the
     patches, do so. If you are pleased with (some of) the patches,
     respond with your Reviewed-by, per patch. (Or, well, if you like it
     all, to the cover letter.)

     If you agree with a patch, more or less, but lack the expertise to
     review it in depth (possible for patches that target a package that
     you don't maintain), respond with your Acked-by.

3.   When reviewing a v2, v3, ... posting of a series, focus on the
     changes. The contributor is expected to support you in this with:

     - Picking up your Reviewed-by and Acked-by tags from your v1
       review. You can skip re-reviewing those patches in v2,
       especially because contributor step (29) instructs the
       contributor to drop your earlier R-b or A-b if he or she
       reworks the patch significantly.

     - Listing the relative changes per patch, in the git-notes section.
       Refer to contributor step (30).

     - Summarizing the changes in the v2, v3, ... cover letters. Refer
       to contributor step (32).

4.   Assuming the series has converged (i.e., all patches have gained
     the necessary Reviewed-by and/or Acked-by tags), plus you have been
     "elected" as the lucky maintainer to apply and push the series,
     read on.

     (The following steps are also relevant if you would like to *test*
     the series, or if you would like to review each patch in the series
     against a fully up-to-date, complete codebase, with all the
     precursor patches from the series applied.)

5.   The first attempt at applying the contributor's series is directly
     from emails. For this, you *absolutely* need a mail user agent
     (MUA) that allows you to save patch emails *intact*.

     So save all the patch emails into a dedicated, new folder.

6.   Refresh your local master branch.

       git checkout master
       git pull

     Note that it is *extremely* important to switch to the master
     branch, with the checkout command above, before you run "git pull".

7.   Create an application/testing/review branch, and apply the patches
     from the files you saved in maintainer step (5), from within your
     MUA:

     ```
       git checkout -b REVIEW_implement_foo_for_bar_vN master

       git am dedicated_directory/*.eml
     ```

     Now, this step can genuinely fail for two reasons. The first reason
     is very obscure and I'm sharing it only for completeness.

     So the first reason is that the patch may create or delete files,
     which implies "/dev/null" filenames in the git diff hunk headers.
     Because of the "core.whitespace" setting in contributor step (05)
     -- which we absolutely need due to the source files using CRLF
     line terminators in the *internal* git representation --, git-am
     might choke on those "/dev/null" lines. This depends on the
     Content-transfer-encoding of the email that is saved in
     maintainer step (5).

     The second reason is that the master branch may have genuinely
     diverged from where it was when the contributor prepared his or her
     patches, on top of then-master. And the patches may no longer apply
     with git-am on top of current master.

     If "git am" above fails for *any reason at all*, immediately issue

     ```
       git am --abort
     ```

     and proceed to the next step, maintainer step (8). Otherwise, if
     "git am" succeeds, skip forward to maintainer step (11).

8.   As an alternative to maintainer step (7), here we'll grab the
     contributor's patches from his or her personal GitHub repo.

     First add his or her personal repo as a *remote* to your local
     clone (this step only needs to be done once, during all of
     the collaboration with a given contributor):

     ```
       git remote add --no-tags \
         HIS_OR_HER_GITHUB_ID \
         https://github.com/HIS_OR_HER_GITHUB_ID/edk2.git
     ```

     At this point you should of course use the repo URL that the
     contributor shared in his or her cover letter, in contributor
     step (23) or -- for a v2, v3, ... post -- in contributor step
     (32).

9.   Fetch any new commits and branches from the contributor's repo:

     ```
       git fetch HIS_OR_HER_GITHUB_ID
     ```

10.  Now, set up a local, non-tracking branch off of the contributor's
     relevant remote branch. You know about the relevant branch again
     from the contributor's steps (23) or (32), i.e., the cover letter.

     ```
       git checkout -b --no-track \
         REVIEW_implement_foo_for_bar_vN \
         HIS_OR_HER_GITHUB_ID/implement_foo_for_bar_vN
     ```

11.  Rebase the contributor's series -- using your local branch that
     you created either in maintainer step (7) or in maintainer step
     (10) -- to the local master branch (which you refreshed from
     upstream master in maintainer step (6)):

     ```
       git rebase -i master REVIEW_implement_foo_for_bar_vN
     ```

     Here you should mark those patches with "reword" that have received
     Reviewed-by, Acked-by, Tested-by tags on the mailing list after the
     contributor's last posting. (Patches that garnered such tags in
     earlier versions are supposed to carry those tags already, due to
     contributor step (28).)

     When rewording the relevant patches, simply append the relevant
     R-b, A-b, T-b tags from the mailing list feedback. (Refer to
     contributor step (28).)

     Now, this rebase has a much better chance to succeed than "git
     am" in maintainer step (7), for two reasons again. The first
     reason is that the problem with the /dev/null headers just
     doesn't exist. The second reason is that "git rebase", *unlike*
     "git am", knows *whence* you are rebasing, which helps it
     immensely in calculating conflict resolutions automatically.

     Nonetheless, the rebase might still fail, if meanwhile there have
     been intrusive / conflicting changes on the upstream master branch.
     If that happens, you can try to resolve the conflicts yourself, or
     you can ask the contributor to rebase his or her work on current
     upstream master, and to post it as the next version.

12.  Okay, now you have the contributor's patches on top of your local
     master branch, with all the tags added from the mailing list. Time
     to build-test it! If the build fails, report it to the list, and
     ask the contributor for a new version.

     (The OCD variant of this step is to build-test the contributor's
     series at *each* constituting patch, to enforce bisectability.)

13.  Okay, the build test passes! Maybe you want to runtime test it as
     well. If you do, and it works, you can respond with a Tested-by
     to the entire series on the list, *and* immediately add your own
     Tested-by to the patches as well. Employ maintainer step (1)
     accordingly.

14.  Time to push the patches to upstream master. Take a big breath :),
     and run

     ```
       git push origin REVIEW_implement_foo_for_bar_vN:master
     ```

     This will *attempt* to push the commits from your local
     REVIEW_implement_foo_for_bar_vN branch -- which is based off of
     your local master branch -- to the main github repo, *and* move the
     upstream master branch forward to the final commit among those.

     If it succeeds, you deserve an alcoholic (or non-alcoholic) drink
     of your choice, you're done.

     If it fails, then the reason is that *another maintainer*
     executed these steps in parallel, and moved forward the upstream
     master branch *after* your maintainer step (6), but before your
     maintainer step (14). If github accepted your push in this case,
     that would cause the *other* maintainer's push to go lost. So,
     proceed to the next step.

15.  Repeat the following steps:

     - maintainer step (6) -- Refresh your local master branch.

       *Do not forget* the "git checkout master" part in that step!

     - maintainer step (11) -- Rebase the contributor's series.

       No changes should be necessary, but if conflicts are found,
       those are due to the fresh commits pushed by the other
       maintainer, mentioned in maintainer step (14). The possible
       remedies are discussed in maintainer step (11) -- fix up the
       conflicts yourself, or ask the contributor to rebase and post a
       new version.

     - maintainer steps (12) through (14) -- rebuild, optionally
       retest, try pushing again.

