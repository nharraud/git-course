Short GIT and GITHUB course showing most common use cases.
==========================================================

The goals is not to list all possible commands. Other tutorials do this
very well. ex: https://github.com/rogerdudler/git-guide

USE CASE 1: Something is broken in my clone. Did this bug exist before my changes?
----------------------------------------------------------------------------------
- Stash your changes.
```sh
$ git stash
```
This creates a temporary branch in which all your changes are stashed

- Checkout the commit your want to check if it isn't the last one.

```sh
$ git checkout <SHA1/LABEL/BRANCHNAME>
```

- Test your code

- You can then get back your changes by returning to the commit you want to
check and running:

```sh
$ git stash pop
```

The modifications stashed away by this command can be listed with
`git stash list`, inspected with `git stash show`, and restored
(potentially on top of a different commit) with `git stash apply`. 
[From the GIT manual](https://git-scm.com/docs/git-stash)


USE CASE 2: Oops made a mistake in my last commit. I want to modify the files or the commit message.
------------------------------------------------------------------------------------------------

```sh
$ git commit --amend
```

If you have already pushed the commit on a remote branch you will need to use
`--force` when pushing next time. Use this cautiously, you are potentially
putting everybody out of sync. Thus DON'T USE THIS IF YOU PUSHED ON A BRANCH
USED BY OTHER PEOPLE.

GITHUB TIP: However there is no problem to force push a pull request branch if
it is on your local repository, the pull request will update automatically. Thus
you can always fix your pull requests, no need to create new commits on top of
it as this makes the pull request hard to read.


USE CASE 3: Oops made a mistake in a commit which IS NOT the last one.
----------------------------------------------------------------------

No worry, use `git rebase --interactive` (or `git rebase -i`)

Example:

First clone this repository.

Then checkout the branch corresponding to this exercise.
```sh
$ git checkout ex-interactive-rebase-branch
```

There are 3 commits on this branch. The second one added a mistake in
`rebase-me-file.txt`

Let's fix this.

First call the interactive rebase command.

```sh
$ git rebase -i 4e06615f18b3d18a098e101f5db14cfea6f29c18
```
Where the SHA1 is the one of the first commit we want to rebase on,
i.e: `EX Interactive Rebase: first commit`

Your editor should open and show you this:

```
pick 0a0d515 EX Interactive Rebase: wrong commit
pick a257f27 EX Interactive Rebase: last commit

# Rebase 4e06615..a257f27 onto 4e06615 (2 command(s))
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

Change the first line like this:

```
e 0a0d515 EX Interactive Rebase: wrong commit
pick a257f27 EX Interactive Rebase: last commit

# ...
```

Then save and quit the editor. The rebase will start. It will stop on the first
commit and tell you that you can amend it.

- remove the wrong line from `rebase-me-file.txt`.
- call `git add rebase-me-file.txt` just like for a normal commit.
- call `git commit --amend`.
- change the message commit if you want.
- call `git rebase --continue` in order to continue the rebase.

You just edited the second commit, changing the whole branch history.

Now revert your branch to it's old state (the one before your rebase) by
calling:
```sh
$ git reset --hard origin/ex-interactive-rebase-branch
```

This command enables you to tell that the current branch you are on should in
fact be exactly at the same position as the remote one, i.e
`origin/ex-interactive-rebase-branch`. You can use reset to set a branch at
any position in your git history by just giving the SHA1 instead of a branch
name.


USE CASE 4: I want to delete a commit, merge multiple commits, reorder my commits.
----------------------------------------------------------------------------------

Use again `git rebase --interactive` (see USE CASE 3). You just have to read
the possible commands displayed in the interactive rebase message comments.


USE CASE 5: I want to do a diff between branches and then apply it.
-------------------------------------------------------------------

Call `git diff <SHA1 OR BRANCH NAME> <SHA1 OR BRANCH NAME>` to get the diff
between the two branchs top commits. You can restrict the diff to a specific
file by giving it to the command.
Example:
```sh
$ git diff <SHA1 OR BRANCH NAME> <SHA1 OR BRANCH NAME> myfile.txt > patch.txt
```

Then apply the patch by calling:
```sh
$ git apply patch.txt
```


USE CASE 6: I want to commit just a few lines out of many changes I did in a file.
----------------------------------------------------------------------------------

Be happy, this is possible with GIT. This for example happens when you have
debug code which you don't want to remove locally but which should not be
committed.

There are many ways to stage the lines you want to commit, command line or
advanced GUIs (git gui, sourcetree, etc...)

- stage the lines or "hunks" you want to commit.
- `git commit`
- `git stash` in order to have only commited code.
- test that you didn't forget to commit anything.
- push the commit to your remote server.
- `git stash pop` to retrieve your uncommited changes and continue your work.


USE CASE 7 [GITHUB]: I want to change commits already pushed in a pull request.
--------------------------------------------------------------------------------

Use `git push <remote name> <branch name> --force` in order to override the
remote branch.

Github will detect the change and update the pull request using the branch.

If you want to replace the branch with another one, use `git reset ... --hard`
as shown above or delete and recreate the branch (with the same name) before
pushing it.


USE CASE 8 [GITHUB]: I want to test locally a pull request
----------------------------------------------------------

Same as described on [this github page](https://help.github.com/articles/checking-out-pull-requests-locally/#modifying-an-inactive-pull-request-locally).

```sh
$ git fetch origin pull/ID/head:BRANCHNAME
```
Where `ID` is the ID of the PULL request (starting with a `#`) and BRANCHNAME
is the local branch you will create.

You can then checkout the branch and test it.

```sh
$ git checkout <BRANCHNAME>
```

CONFIGURATION
-------------

I recommend to add this to your `.gitconfig` file:

```
[pull]
  rebase = true
```
This tells git to rebase when pulling a branch.

```
[merge]
  ff = only
```
This tells git to merge only when the two branches are fast forward. You will
have to rebase if it is not the case.

