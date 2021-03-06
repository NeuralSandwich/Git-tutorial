# Git Basics

* [Creating Projects](#creating-a-new-project)
* [Checking Repository Status](#checking-the-status)
* [Staging & Commiting](#adding-content--committing)
* [History Log](#viewing-history)
* [Removing Files & Mistakes](#removing-reverting-resetting--cleaning)
* [Branching](#branches)

## Advanced Topics
* [Rewriting History](#rebasing)

## Creating a New Project

To start using Git first we need to be in a repository. We will initialize a new
one, to do this we will need to run `git init`. To follow this tutorial, you
will need to be in a terminal with Git installed or using the Windows Git Bash.

To get started run the following commands:

```
mkdir git-tutorial
cd git-tutorial
git init
```

You can also initialize projects using `git init <directory>`, this will create
the git repository in the specified repository. Running either of the two
commands should produce the following output from Git:

```
Initialized empty Git repository in .git/
```

## Copying Projects

Many open source projects already use git ([github](http://github.com)). To work
on any of them you need to clone the project, which creates a copy of the
repository. You can do this with `git clone <url>` but for now we will move on.

## Checking the Status

Now that we have initialized the repository, we can check the status of the
repository. If you ever wish to see the state of the working directory or
staging area just run `git status`, let's do this now.

```
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)
```

## Adding Content & Committing

Now that we can get `git` to tell us the status of the repository, lets create
some content for git to keep track of.

```
echo "foo" > foo.txt
echo "bar" > bar.txt
```

By adding files to the working directory of the repository we have changes its
status. Lets check how the change the status of the repository, run `git
status` again

```
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

    bar.txt
    foo.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Adding these files changed the working directory of our repository, we can use
the `git add <file>` command to add these changes to the staging area. This
is an area which tracks which changes to the working directory will be in the
next commit. The staging area doesn't change the repository as these changes
won't actually be recorded until you commit them. This is one of Git's more
unique features, it allows you to craft your commits splitting changes into
relevant groups, instead of just saving all of your changes since the last
commit.

To add your changes run `git add .`, this command adds all changes to files in
the repository (but not deleted files). Now check the result by running
`git status` again. You should see the following output:

```
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   bar.txt
    new file:   foo.txt

```

`git add` can also be used to add directories in the same manner as files, it
is simple `git add <directory>`.

Now that you have some changes in the staging area, we can go ahead and create
your first commit: `git commit -m "Initial commit"`. The `-m` tells `git commit`
that you are giving it a message on the command line (without it this will open
the default text editor set in your `.gitconfig`, but we will ignore this
detail).

Now our changes that were in the staging area have been stored as a commit. If
you run `git status` again you will see the following message.

```
On branch master
nothing to commit, working directory clean
```

If you make a mistake when writing a commit message use `git commit --amend`, this
will allow you to edit the commit message.

## Viewing History

All version control systems including Git show a projects commit history. The
`git log` command displays the commited snapshots, showing your commit history.
If you run `git log` you should see similar output:

```
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 15:38:15 2014 +0100

    Initial commit
```

Let's create some more commits so we can see what `git log` looks like when
populated with more than one commit.

```
echo "foobar" > foobar.txt
echo "foo is not bar" > foo.txt
echo "bar is not foo" > bar.txt
```

Instead of using `git add .`, use `git add -p` You should be prompted with output
similar to this:

```
diff --git a/bar.txt b/bar.txt
index 5716ca5..02626e7 100644
--- a/bar.txt
+++ b/bar.txt
@@ -1 +1,2 @@
 bar
+ is not foo
Stage this hunk [y,n,q,a,d,/,e,?]?
```

Enter `y` and then `git commit -m "Add changes to foo & bar"`, finally do the
following:

```
git add foobar.txt
git commit -m "Add foobar.txt"
git log
```

Hopefully, you should see similar output:

```
commit bff897c9d3d27cfee74516935500388b417f1c11
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:50:14 2014 +0100

    Add foobar.txt

commit 0c5275028bd818395de035aa1733a3f2889532e5
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:45:13 2014 +0100

    Add changes to foo & bar

commit 36a24afa1548fb767439f36e3e8bee8458f571ee
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:23:27 2014 +0100

    Initial commit
```

This output is fairly simple to understand, every commit has a 40 character SHA1
hash, which is a checksum for the commit and a unique ID. Running `git log
--decorate` will show some extra output.

```
commit bff897c9d3d27cfee74516935500388b417f1c11 (HEAD, master)
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:50:14 2014 +0100

    Add foobar.txt
```

This extra output `(HEAD, master)` indicates that the latest commit `bff897c`
is the current commit and that it is the tip of the master branch. You can use
the `HEAD` reference and branch names with Git commands that require a commit
id.

Try some of the following commands, see the different output they produce:

```
git log -n <limit>
git log --oneline
git log --stat
git log -p
git log --author"<pattern>"
git log --grep="<pattern>"
git log <since>..<until>
git log <file>
git log --graph --decorate --oneline
git log --graph --stat -p --decorate
```
Using `git log <since>..<until>` command allows you to seen between the
specified range. For example `git log HEAD~2..HEAD`:

```
commit bff897c9d3d27cfee74516935500388b417f1c11
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:50:14 2014 +0100

    Add foobar.txt

commit 0c5275028bd818395de035aa1733a3f2889532e5
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sat May 10 22:45:13 2014 +0100

    Add changes to foo & bar

```
The `~` character is used for referencing the relative parent of the specified
commit. HEAD~1 being the commit previous to HEAD and bff897c~1 being 0c52750,
the commit before bff897c. For more information about `git log` visit [here]
(http://git-scm.com/docs/git-log)

## Removing, Reverting, Resetting & Cleaning

Now that we have some history in the repository, let's use it for what it is
mean to do, store file versions safely. Run the following command `cat foo.txt`
you should see:

```
foo is not bar
```

If you wanted to use the original file, or just inspect what it contained you
can run `git checkout HEAD~2 ` `foo.txt` is now in original state. Running `cat
foo.txt` should yeild the original contents:

```
foo
```

`git status` will show that `foo.txt` has been modified compared to HEAD (the
current commit).

### Resetting

To redo the changes made to `foo.txt` there are several options:

```
git checkout foo.txt
git reset HEAD foo.txt
git reset --hard HEAD
```

Try a command out then run `git status` to confirm it has been restored to the
latest version (the second command will not full restore the file, the third
command will). Use `git checkout HEAD~2` to set `foo.txt` back to the first
version and try the next command. If `foo.txt` has been restored to the latest
version you should see the following:

```
On branch master
nothing to commit, working directory clean
```

The first command in the list, creates a `foo.txt` from the snapshot at HEAD,
replacing the contents of the file. The second command resets any changes that
were in the staging area for that file, all the changes are still present in
the file. The last command will restore all tracked files in the staging area
and the working directory to those in the HEAD commit.

### Removing Changes & Files

When making changes to your files, occasionally you might want to remove the
the change in the case where you added a mistake. Run the following commands:

```
echo "foobar is neither foo nor bar" > foobar.txt
git add foobar.txt
git status
```

Now the change to `foobar.txt` has been added to the staging area, check with
`git status` to confirm:

```
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   foobar.txt
```

Now to remove this change to `foobar.txt` from the staging area, you can use
`git reset HEAD <file>` to remove changes to files from the staging area.
To remove the change to `foobar.txt` run `git reset HEAD foobar.txt`.


```
echo "foobar is neither foo nor bar" > foobar.txt
git add foobar.txt
git commit -m "Add changes to foobar"
```

Say removing `foobar.txt` was a mistake, we can reset the index of the
repository, essentially rewinding history. Let's try it out:

```
git rm foobar.txt
git commit -m "Remove foobar"
```

To rewind this commit we can run the following command `git reset HEAD~1`,
this will reset the index to the previous commit.

WARNING: Only do this on commits to your local repository. DO NOT do this
when a commit has come from a remote repository, it LITTERALLY rewrites
history.

### Reverting

The correct way to undo a commit is to use `git revert`. This command creates
a commit which will undo the changes of a specified commit. Let's try it `git
revert HEAD`, reverting HEAD (the lastest commit) should result in the
following `git log`:

```
commit 5b4c1b41f75673407c6965466b9e7dbe96d074d4
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sun May 11 09:28:22 2014 +0100

    Revert "removed foobar"

    This reverts commit d3840ad88286808062169c1a201316a00d85f880.

commit d3840ad88286808062169c1a201316a00d85f880
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sun May 11 03:04:09 2014 +0100

    removed foobar
```

We can see better what has happened by using `git log -p --oneline HEAD~2..HEAD`

```
5b4c1b4 Revert "removed foobar"
diff --git a/foobar.txt b/foobar.txt
new file mode 100644
index 0000000..4768904
--- /dev/null
+++ b/foobar.txt
@@ -0,0 +1 @@
+foobar is neither foo nor bar

<inserted space for clarity>

d3840ad removed foobar
diff --git a/foobar.txt b/foobar.txt
deleted file mode 100644
index 4768904..0000000
--- a/foobar.txt
+++ /dev/null
@@ -1 +0,0 @@
-foobar is neither foo nor bar

```

We can see in these diffs that `git revert HEAD` has create a commit that does the
exact opposite of the previous commit, it has added `foobar.txt` back. This is the
correct way to undo commits if they have been pushed to public projects.

## Cloning & Remote Repositories

The `git clone` command was previously mentioned in [Creating Projects]
(#creating-a-new-project). The `git clone <url>` command can be used to create exact
copies of repositories, either on your machine or remote locations.

Let's try cloning the repository that has been used as an example throughout this
tutorial.

```
cd ..
git clone https://github.com/NeuralSandwich/git-tutorial-basic.git
```

Following output:

```
Cloning into 'git-tutorial-basic'...
remote: Counting objects: 15, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 15 (delta 3), reused 15 (delta 3)
Unpacking objects: 100% (15/15), done.
Checking connectivity... done.

```

Congradulations, if you so the following output then you have successfully created
a clone of the repository.

Before the next section, we need to do some house keeping. Run the following
command `rm -rf git-tutorial`:

### Bare Repositories

Remote repositories are normally stored as bare repositories. Bare repositories
do not have working directories. These repositories just store all the
information from inside the `.git` directory. They are set out like this:

```
.
├── branches
├── config
├── description
├── HEAD
├── hooks
├── info
├── objects
├── packed-refs
└── refs

5 directories, 4 files
```

Since, you will not have access to push changes to the repository you just
cloned, you need to create a local bare repository to use for the rest of the
tutorial.

```
git clone --bare git-tutorial-basic
```

This should produce the following output:

```
Cloning into bare repository 'git-tutorial-basic.git'...
done.
```

### Pushing & Pulling

Let's go back to our repository and get it working with our bare repository. All
of the following commands will work the same even if the repository was an
actual remote repository. The only differene would be the URL to access it.

```
cd git-tutorial-basic
git remote set-url origin ../git-tutorial-basic.git
```

When cloning the repository Git will also set a remote connection called origin
This was set to pull from GitHub.com. `git remote set-url <name> <url>` This
command tells Git to change the URL of the remote connection origin to
`../git-tutorial-basic.git` we need to do this so we can use the new bare
repository as a remote connection.

```
git push -u origin master
```

This should produce similar output:

```
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 359 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To ../git-tutorial-basic.git
   7a2bf50..cd10233  master -> master
```

`git push` send your commits to the remote repository. To get changes from the
remote server you can use `git fetch` and getting the changes and applying them
to the working directory you can use `git pull`

## Branches

+Git's more popular feature is its support of light weight branching. A branch is
+an abstract representation of an independent line of development. It allows you
+to make changes to your code, as if you were being given new working directory,
+staging area and project history. However you can instantly switch to others
+which may have other development in them.
 
So far in this tutorial, you have been working exclusively one `master`, the
default branch name. To create and switch to a new branch there is two methods:

```
git branch <name>
git checkout <name>
```

Or.

```
git checkout -b <name>
```

Both of these create a new branch and switch the working directory to it.
Creating branches for specific tasks such as bug fixes, adding features and
refactoring code are common practise. It allows several lines of development to
be carried out in parrallel without conflict.

Once a line of development is complete, it should be merged into the appropriate
branch and then deleted. This can be done using `git branch -d <name>` if a
branch has not been merged but you still wish to delete it use `git branch -D
<name>`

Switching between branch is as simple as calling `git checkout <name>` this will
switch HEAD to the relevant snapshot.

To learn more about branch visit the great interactive 
[LearnGitBranching](http://pcottle.github.io/learnGitBranching/) tutorial by
Peter M Cottle.

# Advanced Git

## Rebasing

Git rebasing, it a very powerful tool. It can be be used to rewrite your
repositories history, for better or worse. `git rebase` does this by copying
commits from a branch and copying them to new base commit.

![Git Rebasing Image](https://gp1.wac.edgecastcdn.net/8029C4/wac-small/wac/landing/git/tutorial/rewriting-git-history/pageSections/00/contentFullWidth/0/tabs/01/pageSections/0/contentFullWidth/00/imageBinary/git-tutorial_rebase.png)

Let's `git rebase <base>` to understand how it works.

```
cd ../
git clone https://github.com/NeuralSandwich/git-tutorial-rebasing.git
cd git-tutorial-rebasing
```

If you run `git log` you will see that this repository has an extra commit on
the master branch compare to the last repository

```
commit f172eb1d904be0621d4152f2bdde0b5d89433152
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Mon May 12 07:48:38 2014 +0100

    Add barfoo.txt

commit 7a2bf50939c737a2bb5fa47d2a2763b692adda79
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sun May 11 09:48:50 2014 +0100

    Remove foobar
```

By running `git branch -v` we can see there is more than one branch.

```
  bring-back-foobar b24dcb0 Revert "Remove foobar"
* master            f172eb1 Add barfoo.txt

```

It appears both have different tip commits. `git log bring-back-foobar` reveals
that the history is also different between these branchs:

```
commit b24dcb0b0da31ba7cfe84dde0298b35f0e0213d4
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Mon May 12 08:02:25 2014 +0100

    Revert "Remove foobar"
    
    This reverts commit 7a2bf50939c737a2bb5fa47d2a2763b692adda79.

commit 7a2bf50939c737a2bb5fa47d2a2763b692adda79
Author: Sean Jones <neuralsandwich@gmail.com>
Date:   Sun May 11 09:48:50 2014 +0100

    Remove foobar

```

If we wanted to merge `bring-back-foobar` into master, we would end up with a
non-linear history. `git log --graph --oneline`

```
*   fcfb589 Merge branch 'bring-back-foobar'
|\  
| * b24dcb0 Revert "Remove foobar"
* | f172eb1 Add barfoo.txt
|/  
* 7a2bf50 Remove foobar
* fff6840 Add changes to foobar
* 2042f6c Add foobar.txt
* 9085b36 Add changes to foo & bar
* 2f7907c Initial commit
```

While this in itself is not a bad thing, for the trivial
change that this branch brings, it doesn't need to introduce a mess into the
history. Let's fix this so when we do merge, we can get a nice linear history.

```
git co bring-back-foobar
git rebase master
```

Now if we look at the history of this branch we will see a nice linear series of
commits. `git log --graph --oneline`

```
* da75a4c Revert "Remove foobar"
* f172eb1 Add barfoo.txt
* 7a2bf50 Remove foobar
* fff6840 Add changes to foobar
* 2042f6c Add foobar.txt
* 9085b36 Add changes to foo & bar
* 2f7907c Initial commit
```

Now if we merge bring-back-foobar into master, it will fast-forward and create
the nice linear history we can see above.

And that is rebasing, for more challenging uses try out the great interactive 
[LearnGitBranching](http://pcottle.github.io/learnGitBranching/) tutorial by
Peter M Cottle. It contains exercises in rebasing, branching and merging that
will help you better understand the process.

