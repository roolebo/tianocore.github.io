New to git? The git site has some great documentation:

* https://git-scm.com/doc

And, if you've worked with svn in the past, this page might be
helpful:

* https://git-scm.com/course/svn.html

EDK II git workflows
--------------------

See [[EDK II Development Process]]

Some Potentially Dangerous Commands
-----------------------------------

Similar to using the dangerous `rm -rf` or `del /s` commands, there
are some aspects of git usage you should be careful with. Here are a
few commands to be careful with.

* `git reset --hard`

  * This command takes your current branch (head) and sets it to
    another revision. By default, it sets it to the last commit on the
    current branch.

  * Why it is potentially dangerous:

    * It will overwrite any change in files that you have not
      committed.

    * It can move your branch to somewhere else, and make you lose
      commits.

  * Ways to recover:

    * Uncommitted changes in files: *NONE*. There is no way to recover
      these lost changes!

    * Lost commits: There is a good chance you can recover the commits
      with the `git reflog` command.

* `git clean`

  * This command deletes files in your local tree that are not tracked
    in your git repository.

  * Why it is potentially dangerous:

    * It can delete files for new features that you have not yet added
      to git.

  * Ways to recover:

    * *NONE*. There is no way to recover the deleted files!

  * Tip:

    * Use the `--dry-run` parameter to see what will be deleted.

* `git merge`

  * This command joins together the histories of two branches.

  * For now, EDK II is choosing to maintain a linear history, and not
    use merges.

  * Why it is potentially dangerous:

    * It is occasionally possible for git to choose the wrong action
      when auto-merging the two branches. Although rare, this can lead
      to some changes getting dropped in the latest tree.

  * Ways to recover:

    * If the merge has not be pushed upstream, there are a few ways to
      recover.

      * The best guaranteed way to fix things is to look through the
        history to find the tree version before your merge. (Helpful
        tools: `gitk`, `tig`, or `git log --oneline --graph`)

      * Now, use `git reset --hard <good-version>`

        * This will force your branch back to the state before the
          merge. Be sure to understand the dangers of `git reset
          --hard` as documented above.

      * You might also be able to simply use `git rebase
        origin/master` to remove the merge commit.

    * If the merged commit is pushed upstream, then unfortunately it
      will persist in history. This is not really a big deal, but just
      be sure that the merge worked correctly. If you find that
      changes were actually lost then add new commits to re-apply the
      changes.

* `git pull`

  * By default, the `git pull` command is a `git fetch` followed by a
    `git merge`. Therefore it has the same concerns as the `git merge`
    command documented above.

  * Tip:

    * You can also run `git config pull.rebase true` to set your edk2
      tree up so that `git pull` will be a `git fetch` followed by a
      `git rebase` (rather than `git merge`)

* `git push -f`

  * This command 'force' pushes a branch. This potentially can force
    the remote branch to rewrite its history.

  * Note: The main edk2 tree is protected from force pushes, and you
    can setup your github branches to be similarly protected:

    <https://github.com/blog/2051-protected-branches-and-required-status-checks>

  * Why it is potentially dangerous:

    * If you 'rewrite' the history of a tree, then people will be
      suspicious that bad changes have been snuck into the tree.

    * This is generally only considered bad for 'upstream' branches
      that many people are basing their work off of.

  * Ways to recover:

    * You might be able to force push the old version back if you can
      find out its version.

  * It's sometimes okay to force push

    * For your personal development branches where no one is depending
      on the branch, it is okay to force push. In fact, most people
      will eventually find that force pushing is a good way to backup
      the currect state of their development work on a remote server.
