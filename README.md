Git-Flow
========

This document outlines a workflow _in explicit detail** on how to perform a pull
request for those unfamiliar with git. For those familiar with `git** all of the
below can be summarized in the following sentence

> A central upstream repository is forked off by developers to create their own
> remote origins that are each developed separately. For every new feature a
> tracking branch is setup on remote origin that will be submitted as a pull
> request into upstream/master. To avoid foxtrot-merging, the upstream/master
> and origin/master branches are kept synced and a final rebase onto a fetched
> upstream/master is performed to squash results into one atomic commit.

or in super succinct bullet form

1. Fork off of upstream repository
2. Clone down and create tracking branches
3. PR branches on origin into upstream
4. Use rebasing to form atomic commits at the end of a PR.

If the above sounds like gibberish this document expands on it by providing a
tutorial for the user to follow along with. There really is no substitute for
trying things out yourself and learning by mistakes so the reader is encouraged
**on their own personal github** to follow this tutorial and resolve any
mistakes that they make along the way as a learning process. Using a personal
github ensures that repositories at will and that any of their accidental
destruction mitigates any loss of business value.

Quick Questions
---------------

### What is Gitflow?

Gitflow is an amalgamated term of "git" and "workflow".

### What is "git"?

Git is a distributed version control system that allows multiple people to
contribute to one central repository. Version control is the process by which
changes to a document are managed in a clear and interpretable manner. As such
there exists many "good practices" and "bad practices" for how to version
control. Sometimes `git` can seem overwhelming to newcomers precisely due to the
number of opinions on what should/should not be done.

### What is a workflow?

A workflow is an _orchestrated, repeatable pattern enabled by systemization_.
The key word here is _systemization_ that means that there will inevitably be
some loss of autonomy when contributing to a workflow as one must follow a
procedure in a way that was previously agreed upon. It is the aim of this
document to provide exactly this systemization.

### Where can I learn more about git

If at any point in this `README.md` you find yourself lost as to what a command or
it's corresponding flags does you can look it up on [git-scm's
documentation](https://git-scm.com/doc), their [tutorial
section](https://git-scm.com/docs/gittutorial) may be of benefit as well.

Outline of Gitflow Procedure
-----------------------------

This document can be dropped into an empty directory and the subsequent commands
can be run to make an interactive procedure to aid in learning. For the sake of
this tutorial we will assume this document is named `README.md`. All commands
are run in `bash`. For generalization purposes sometimes outputs are rewritten
with variable outputs depending on the user or particular configuration.

### Getting Started

To start any `git` project we need to (init)ialize the repository in the
following manner

```
> git init
Initialized empty Git repository in <PRESENT_WORKING_DIR>/.git/
```

this creates a hidden directory called `.git` which helps us record and control
the various versions of the software in the containing directory. If you look
around you should see this folder.

```
> ls -la
drwxr-xr-x   4 <USER> staff   128B <DATETIME> .
drwxr-xr-x  12 <USER> staff   384B <DATETIME> ..
drwxr-xr-x   9 <USER> staff   288B <DATETIME> .git
-rw-r--r--   1 <USER> staff   1.8K <DATETIME> README.md
```

If you have `clone`d down a repository from an online host such as GitHub,
GitLab or BitBucket it will ensure that a `.git` directory file is present and
there is no need to perform the `init`. With this not a `git` repository we can
look around to check the state of the repository with a `status` report.

```
> git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	README.md

nothing added to commit but untracked files present (use "git add" to track)
```

This `status` report tells us a few things

1. First block we are on `branch` "`master`", we will get to what `master` means
   in a bit
2. There have been no commits yet added
3. We have untracked files that have not been versioned yet

We can verify that we are on `master` (or in general check which `branch` we
currently are on) by using the `branch` command.

```
> git branch
```

However, because there are no `commit`s yet the branch doesn't even exist! So
let us `add` a file to be tracked and then `commit` it with a message using the
`-m` flag.


```
> git add README.md
> git commit -m "Add README.md"
[master (root-commit) 43e8987] Add README.md
 1 file changed, 102 insertions(+)
 create mode 100644 README.md
```

Now if we check our branches we see `master`

```
> git branch
* master
```

We can also see the `commit` and it's corresponding message by checking the
`log`s of git

```
> git log
commit <HASH> (HEAD -> master)
Author: <AUTHOR NAME> <EMAIL>
Date:   <DATETIME>

    Add README.md
```

Some people prefer to use a more compact representation especially when dealing
with many branches.

```
git log --oneline --graph --all --decorate
* 43e8987 (HEAD -> master) Add README.md
```

Branching and Heads
-------------------

Up to this point we have now created one commit to the "`master`" branch but
what is "`master`". Briefly `master` is the `branch` that other `branch`es are
generated off of to add other features. The reason for this will become clear as
we move on further into this documentation. Let us start for now by simply
creating a new `branch` this is done by using the `checkout` function

```
git checkout -b mynewfeature
M	README.md
Switched to a new branch 'mynewfeature'
```

If we now observe are `branch`es we will see two present

```
> git branch
  master
* mynewfeature
```

The asterisk denotes which `branch` we are currently on. Let's add a file to our
new branch.

```
echo 'Hello world!' > helloworld.txt
```

checking out our status report we see that this file is untracked.

```
> git status
On branch mynewfeature
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	helloworld.txt

nothing added to commit but untracked files present (use "git add" to track)
```

like before let us `add` and `commit` the file.

```
> git add helloworld.txt
> git commit -m "Add file exclaiming hello world."
[mynewfeature ef6c7d3] Add file exclaiming hello world.
 1 file changed, 1 insertion(+)
 create mode 100644 helloworld.txt
```

Let's check the `log` to see how things look now

```
git log --oneline --graph --all --decorate
* ef6c7d3 (HEAD -> mynewfeature) Add file exclaiming hello world.
* 43e8987 (master) Add README.md
```

Here we see that our latest commit `ef6c7d3` is ahead of our previous commit
`43e8987` by one commit (your hashes will differ). The current state of our
repository is indicated by the `HEAD ->` symbol. Currently our `HEAD` points to
the commit `ef6c7d3` commit on `branch` `mynewfeature`. We can observe this by
looking at the directory contents.

```
> ls
README.md      helloworld.txt
```

let us now redirect our `HEAD` to the `master` `branch`. We redirect to our
desired `branch` by using `checkout`

```
> git checkout master
Switched to branch 'master'
```

and we can verify that we have redirected the `HEAD` by looking at the log

```
git log --oneline --graph --all --decorate
* ef6c7d3 (mynewfeature) Add file exclaiming hello world.
* 43e8987 (HEAD -> master) Add README.md
```

We see `HEAD` now currently points to `master` and thus all our previous changes
would not be seen as they were not part of this commit.

```
> ls
README.md`
```

Note the disappearance of `helloworld.txt`. This is useful as we can develop
multiple features at the same time without changes affecting each other. Let's
create another branch.

```
> git checkout -b myotherfeature
Switched to a new branch 'myotherfeature'
```

now if we inspect the log

```
git log --oneline --graph --all --decorate
* ef6c7d3 (mynewfeature) Add file exclaiming hello world.
* 43e8987 (HEAD -> myotherfeature, master) Add README.md
```

we can see that we have a new `branch` called `myotherfeature` that we currently
have our `HEAD` pointing too. If we were to make some changes it too would end
up one commit ahead of master as well.

```
> echo 'Foo Bar' > foobar.txt
> git add foobar.txt
> git commit -m "Add foobar.txt"
[myotherfeature 7aeb6ff] Add foobar.txt
 1 file changed, 1 insertion(+)
 create mode 100644 foobar.txt
```

now if we inspect the log we see this change also made but **branching** off of
master alongside our previous feature branch

```
* 7aeb6ff (HEAD -> myotherfeature) Add foobar.txt
| * ef6c7d3 (mynewfeature) Add file exclaiming hello world.
|/
* 43e8987 (master) Add README.md
```

While we have `branch`ed off of `master` here what we have done can be applied
to any branch of any particular name. The reason why we focused on `master` was
alluded to in that by convention that name has special connotations associated
with it which will be discussed in the next section.

Upstream and Origin
-------------------

To understand the importance of `master` it is easy to have a little
foreshadowing to understand it's use in our gitflow must first introduce two new
terms with connotations associated to them by convention `upstream` and
`origin`. Each term refers to a _whole repository_ that you would find hosted
somewhere, which in the case of Pfizer is called GitHub. Because they are hosted
somewhere else other than your machine they are called `remote` and can be
viewed anytime by running

```
> git remote -v
```

Which up to our current status shows nothing because we initialized it locally
and did not connect it with GitHub!

```
git remote add upstream https://github.com/pfizer/PROJECTNAME.git
```

now if we check our `remote`s we see `upstream` has been added.

```
> git remote -v
upstream	https://github.com/pfizer/PROJECTNAME.git (fetch)
upstream	https://github.com/pfizer/PROJECTNAME.git (push)
```

We can also add an `origin` in the same way

```
> git remote add origin https://github.com/USER/PROJECTNAME.git
> git remote -v
origin	https://github.com/USER/PROJECTNAME.git (fetch)
origin	https://github.com/USER/PROJECTNAME.git (push)
upstream	https://github.com/pfizer/PROJECTNAME.git (fetch)
upstream	https://github.com/pfizer/PROJECTNAME.git (push)
```

Note that the two urls differ by the **user** associated with the repository.
Most of the time when using git you `fork` off of a central repository to gain
your own repository for development. This `fork` creates an identical copy of
the repository **on your user account** that you can then `clone` down

```
git clone https://github.com/USER/PROJECTNAME.git
```

if you were to `clone` it down it would of automatically set up the `origin` url
with the `clone`d url. This is very useful when working with a team of
developers. There exists one central `upstream` repository owned by the company
and a bunch of other repositories `fork`ed off of the `upstream` by the team so
that each team members has their `origin` associate with their account. All the
previous `branch`ing rules apply but they are now denoted with the `remote`
directory specified in front of it with a slash, for example `upstream/master`
indicates the `master` branch of the `upstream` and `origin/master` indicates
the `master` branch of `origin`.

We are now equipped to discuss the special importance of the `master` branch it
is the branch that is meant to mirror the `upstream/<branch>` of interest
usually `upstream/master`. This way newly created `branch`es minimize the number
of changes from the `upstream` repository. Keeping these two branches aligned is
called "syncing a fork" and is such a common practice that some [services offer
it by
default](https://confluence.atlassian.com/bitbucketserver/keeping-forks-synchronized-776639961.html)
however GitHub requires you to [do it
manually](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/syncing-a-fork)
via the following sequence of commands that you should now be prepared to understand.


```
> git fetch upstream
> git checkout master
> git merge upstream/master
```

Your `origin/master` should _never_ be developed on otherwise the `git merge`
step here could get a bit ugly and resolving this (using good practices) is
deemed to be out of scope of this post. While this syncing step is very
important it only needs to be done twice

1. Before you create a new branch to minimize the commits ahead you are of `upstream/master`
2. When performing **pull requests**

The first point should be clear from the previous section that focused on how
`branch`ing works, the latter requires a new section to discuss in detail but
before we learn how to creating a pull request from `origin` into `master` we
need to first become comfortable with `track`ing and `pull`ing branches in
general.

Pulling and Tracking
--------------------

The term for asking to add a contribution to an `upstream` repository is called
a **"pull request"** or PR. Usually a PR is made from a developers `origin` to
an `upstream` repository. In our fictious case this can be viewed by the
`remote` command

```
git remote -v
origin	https://github.com/USER/PROJECTNAME.git (fetch)
origin	https://github.com/USER/PROJECTNAME.git (push)
upstream	https://github.com/pfizer/PROJECTNAME.git (fetch)
upstream	https://github.com/pfizer/PROJECTNAME.git (push)
```

Recall that our local development is a reflection of `origin` and we can check
our `branches ` by running the `log` command

```
* 7aeb6ff (HEAD -> myotherfeature) Add foobar.txt
| * ef6c7d3 (mynewfeature) Add file exclaiming hello world.
|/
* 43e8987 (master) Add README.md
```

Note that up to this point we have not discussed how to align our `local`
environment with `origin` and we can see this by noting that there are log
contains no `remote` information. Let us fix this by associating our `local`
`branch`es with `origin` starting with `master` by `push`ing to our `origin`
directory that is stored `remote`ly. When we first try to `push` we will
encounter an error message.

```
> git checkout master
Switched to branch 'master'
> git push
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master
```

Do not let the flag `--set-upstream origin` confuse you. While we may have by
convention to `remote` directories called `origin` and `master`, in `git`, the
term `upstream` has a more general purpose. In a sense our `origin` directory is
`upstream` to our `local` directory and that is why we set `origin` for our
`--set-upstream` flag. Note that by default `git` is suggesting that we do
exactly this so there is no need for alarm! If we follow `git`s instructions we
will receive another error

```
git push --set-upstream origin master
remote: Repository not found.
fatal: repository 'https://github.com/USER/PROJECTNAME.git/' not found
```

However this time the error comes about because for instructive purposes we have
created phony `origin` and `remote` urls that do not exist so of course they
error out, something would be very wrong if they didn't!!! 

So at this stage we must now [go to GitHub and create an EMPTY 
repository](https://help.github.com/en/github/getting-started-with-github/create-a-repo)
to continue with the remaining part of the tutorial. I created one called
`gitflow` for the purpose of this tutorial. When you create an empty repository
on GitHub you will be met with the following suggestion

```
git remote add origin https://github.com/USER/gitflow.git
git push -u origin master
```

This will fail because we already set up `origin` rather we need to reassign
`origin` (note that here `USER` should be replaced by your actual username)

```
> git remote set-url origin https://github.com/USER/gitflow.git
> git remote -v
origin	https://github.com/USER/gitflow.git (fetch)
origin	https://github.com/USER/gitflow.git (push)
upstream	https://github.com/pfizer/PROJECTNAME.git (fetch)
upstream	https://github.com/pfizer/PROJECTNAME.git (push)
```

Now when we try our push statement we see it go through.

```
> git push --set-upstream origin master
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 1.84 KiB | 942.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/USER/gitflow.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

and if we inspect our `log`.

```
* 7aeb6ff (myotherfeature) Add foobar.txt
| * ef6c7d3 (mynewfeature) Add file exclaiming hello world.
|/
* 43e8987 (HEAD -> master, origin/master) Add README.md
```

we see a **new** `branch` added called `origin/master` that is on the same
`commit` as our `master` `branch` which our `HEAD` is currently pointing to.
Likewise we can track our other directories up our other directories When we set
an `upstream` to a branch thats this is called a **tracking branch**. We can set
up **Tracking branches** for the rest of our local branches as well.

```
> git checkout mynewfeature
...
> git push --set-upstream origin mynewfeature
...
> git checkout myotherfeature
...
> git push --set-upstream origin myotherfeature
...
```

now all our `local` `branch`es have "backed up" versions online in our GitHub
repository. I could if I wanted to delete this repository and re-clone them down
and be able to recover them... so why not do exactly that?


```
> git branch
  master
  mynewfeature
* myotherfeature
> git checkout master
> ls -a
.          ..         .git       README.md  foobar.txt
> rm -rf .git README.md foobar.txt
> ls -a
.  ..
> git clone https://github.com/USER/gitflow.git
...
> mv gitflow/* gitflow/.git .
> rm -rf gitflow
> ls -a
.         ..        .git      README.md
```

At first site it appears we've deleted `foobar.txt` -- but fear not -- note that
when we deleted all our files including the `.git` repository we were on the
`myotherfeature` `branch`. Inspecting the `log` we see that it still exists on
`origin`.

```
* 7aeb6ff (origin/myotherfeature) Add foobar.txt
| * ef6c7d3 (origin/mynewfeature) Add file exclaiming hello world.
|/
* 43e8987 (HEAD -> master, origin/master, origin/HEAD) Add README.md
```

and we can pull it down to `local` by specifying the branch we want

```
> git checkout -b myotherfeature
Switched to a new branch 'myotherfeature'
> git pull origin myotherfeature
From https://github.com/USER/gitflow
 * branch            myotherfeature -> FETCH_HEAD
Updating 43e8987..7aeb6ff
Fast-forward
 foobar.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 foobar.txt
```

The message tells us that we can see that we `pull`ed down a branch called
`myotherfeature` that resulted in adding one new line (`1 +`) and we can
explicitly confirm the existence of this file by listing the directory.

```
>  ls -a
.          ..         .git       README.md  foobar.txt
```

Pull Requests
-------------

Suppose we now want to join our `branch` `origin/mynewfeature` into
`upstream/master`. The first problem is that we do not actually have an
`upstream` setup. In theory we could go create a new GitHub directory and link
it by using `git remote add upstream <URL>` but for ease of this tutorial recall
that `upstream/master` is just a branch and furthermore that `branch` is
supposed to _always_ be synced with our `origin/master` so we can navigate to
GitHub and open a pull request into our own `origin/master` for educational
purposes, just be sure when it comes to the real deal when opening the pull
request you select `upstream/master` from the drop down menu so you don't look
like a goof!

1. Navigate to repository on GitHub
2. Click on "Pull Requests" tab
3. Click "New Pull Request"
4. Select "mynewfeature" and choose the base branch to be "master"
5. Click "Create Pull Request"
6. Write a title and summary
8. (IF THIS WAS GOING INTO UPSTREAM Select a Reviewer on the right hand of the screen)
9. Click "Create Pull Request"

Congrats you submitted a PR! At this point a notification will be sent to the
review that the PR has been submitted for review. If required, comments can be
made on various parts of the code and then the status of the PR can be marked
and sent back to the original submitter.


Fixups and Rebasing
-------------------

To merge a pull request there needs to be no **conflicts** between the branch
that is being pulled into the base branch. A conflict is a part of the version
history that is contradictory and requires human validation. It could arise from
multiple people editing the same part of the code or somebody accidentally
botching commands that mixed up the internal tracking. A lot of the time
conflicts arise simply because PRs can take a while before they are accepted and
merged and the `upstream/master` branch can drift away from the original
starting point. This is so common that `git` actually has some built in tools to
help with this issue. Suppose on the PR I submitted I got the comment back.

> "Hello world!" is too happy I wanted an inquisitive hello world.

To satisfy this comment we edit our file

```
echo "Hello world?" > helloworld.txt
```

we can verify this difference by running the `diff` command

```
 git diff helloworld.txt
diff --git a/helloworld.txt b/helloworld.txt
index cd08755..24ba056 100644
--- a/helloworld.txt
+++ b/helloworld.txt
@@ -1 +1 @@
-Hello world!
+Hello world?
```

now while this may seem to resolve the comment on our end we should really be
sending it back to the original reviewer to see if it resolves the comment on
their end. To do this we `push` what is called a `fixup` `commit` we create this
by running the following command.

```
> a helloworld.txt
> git commit --fixup HEAD
[mynewfeature c7e9ec2] fixup! Add file exclaiming hello world.
 1 file changed, 1 insertion(+), 1 deletion(-)
> git push
...
```

this automatically creates a commit that has the original name and is prepended
by the word `fixup!`

```
* c7e9ec2 (HEAD -> mynewfeature, origin/mynewfeature) fixup! Add file exclaiming hello world.
* ef6c7d3 Add file exclaiming hello world.
| * 7aeb6ff (origin/myotherfeature, myotherfeature) Add foobar.txt
|/
* 43e8987 (origin/master, origin/HEAD, master) Add README.md
```

Note that both `mynewfeature` and `origin/mynewfeature` are synced because we
`push`ed our commit. If you inspect the Pull Request page you will see this
change was shown there as well. After a series of `fixup`s are made the revision
round can be said to be done and sent back to the reviewer.

Let's assume that this was adequate and no more changes were needed as indicated
by our reviewers comment

> Looks great to me! Rebase and I'll merge after squashed.

The first part indicates that the changes were adequate. The second part
indicates that the revision history needs to be "rebase[d]" which seems to be
the same thing as "squashed". What the reviewer is really saying is that in
practice revision histories should have only **one atomic commit** and here we
have two commits. The one that we originally submitted and the ones that were
done to address the reviewers comments. **What we need to do is rewrite our git
history to make it appear as if there is one atomic commit**. The command for
this is called `rebase` and the use of our `fixup`s previously make it very easy
using something called the `autosquash` command. Let's perform an
**(i)nteractive** `rebase` onto `origin/master`.

```
> git fetch --all
> git rebase -i --autosquash origin/master
```

``` vim
pick ef6c7d3 Add file exclaiming hello world.
fixup c7e8ec2 fixup! Add file exclaiming hello world.

# Rebase 43e8987..c7e9ec2 onto 43e8987 (2 commands)
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
~
~
~
~
~
```

Note that some of the allowed commands are already preselected on the left side.
This is what the `--autosquash` flag does which we used. Note the message says
that lines will be executed from bottom to top. Thus the order of our rebase
will first apply a `fixup` command to `c7e8ec2` and the `pick` `ef6c7d3`. The
only problem is that after the PR revisions the commit message isn't as
representative as it should be. For this case we can use `reword` so that way we
still `pick` commit `ef6c7d3` but stop first to edit the `commit` message.
Navigating in vim we re-write this as

```
reword ef6c7d3 Add file exclaiming hello world.
fixup c7e8ec2 fixup! Add file exclaiming hello world.
```

and the write and exit using `:wq` in. We are met with a second screen that
allows us to edit the commit message in vim which we change to

```
Add file inquisitively saying hello world.
```

to reflect our changes. The `rebase` should now finish successfully and we can
view our changes by looking at the `log`

```
* 9a2b5a0 (HEAD -> mynewfeature) Add file inquisitively saying hello world.
| * c7e9ec2 (origin/mynewfeature) fixup! Add file exclaiming hello world.
| * ef6c7d3 Add file exclaiming hello world.
|/
| * 7aeb6ff (origin/myotherfeature, myotherfeature) Add foobar.txt
|/
* 43e8987 (origin/master, origin/HEAD, master) Add README.md
```

We can see that we have `rebase`d down to one `commit` off of `origin/master`
that is currently on commit `43e8987 `. We now need to **overwrite** the history
of the `origin/mynewfeature` branch that we were tracking with our local
`mynewfeature` branch that has been changed by the `rebase`.

To do this we use force push.

Be careful when you force push as you can destroy, rewrite or corrupt prior
history. Keep in mind the adages that with "great power comes great
responsibility" when you "use the force".

```
git push -f
...
```

inspecting our branch after with `log` we see they are now synced.

```
* 9a2b5a0 (HEAD -> mynewfeature, origin/mynewfeature) Add file inquisitively saying hello world.
| * 7aeb6ff (origin/myotherfeature, myotherfeature) Add foobar.txt
|/
* 43e8987 (origin/master, origin/HEAD, master) Add README.md
```

and if we check the pull request we observe **one atomic commit**.

At this point we would then notify the reviewer that the "PR has been rebased"
and it is ready to be squashed. Keep in mind while our rebase here was performed
onto `origin/master` it will usually be performed onto `upstream/master` which
is the central repository that all devs `fork`ed off of.

Closing Remarks
===============

This document focused solely on the gitflow set up for organizing a team of
developers who wish to contribute to one central repository. This only begins to
scratch the surface of `git` and you may very well have to learn other aspects
to resolve various conflicts, cleanup your history, fix detached heads etc etc.
It is good to note that while StackOverflow can be of use in helping fix a
problem it can also lead to many more problems when blindly copy and pasted.

This document does not discuss continuous integration/continual development or
static/dynamic/unit tests each of which is a topic that is sufficiently
sophisticated that it deserves to be studied separate from gitflow even though
gitflow will inevitably be drawn into the discussion. For next steps learning
about git the reader is encouraged to learn about style conventions such as
[pylint](https://github.com/PyCQA/pylint) and automatic code formatting such as
[black](https://github.com/psf/black) which are concepts separate from git but
when correctly applied minimize conflicts as well as move one in the right
direction of static testing from which they can proceed onto dynamic tests, unit
tests, continuous integration and finally continuous development to understand
software qualities at different levels of generality.
