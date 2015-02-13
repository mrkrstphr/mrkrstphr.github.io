---
layout: post
title: ! "Stop Merging Master"
excerpt: Merging master is stupid and makes you look like a damn fool.
categories:
- Development
tags:
- git
- process
status: publish
type: post
published: true
---

I probably annoy my coworkers by constantly complaining about them merging master. I don't think any of them understand
what I'm talking about. Probably because I just yell and never explain.

So let's walk through a little scenario that explains why merging master is stupid.

Let's create a new repository and make some commits:

    $ mkdir git-test
    $ cd git-test
    $ git init .
    Initialized empty Git repository in /Users/mrkrstphr/Projects/git-test/.git/
    
    $ echo 'foooo' > README.md
    $ git add README.md
    
    $ git commit -m "adding readme"
    [master (root-commit) 0ad500b] adding readme
     1 file changed, 1 insertion(+)
     create mode 100644 README.md
    
    $ echo 'fofoofofof' >> README.md
    $ git commit -am "2. adding"
      1 Merge branch 'master' into foo
    [master 31fd32d] 2. adding
     1 file changed, 1 insertion(+)

Now let's go ahead and create a new branch to do some separate work in:

    $ git checkout -b foo
    $ echo 'asdfasdf' >> LOL
    $ git add LOL
    $ git commit -am "a. first commit"
    [foo 21edb08] a. first commit
     1 file changed, 1 insertion(+)
     create mode 100644 LOL

If we're working with other people, or maybe doing separate work, master might change while we're working on this
`foo` branch:

    $ git checkout master
    Switched to branch 'master'
    
    $ echo '234234234' >> README.md
    $ git commit -am "3. more more more"
    [master f25c8a8] 3. more more more
     1 file changed, 1 insertion(+)

Since we've diverged from `master`, it's quite possible -- and quite common -- that we may want to bring those changes
in `master` to our branch so we can use or incorporate them. 

Conventional and common wisdom says to merge those changes in:

    $ git checkout foo
    Switched to branch 'foo'
    $ git merge master
    Merge made by the 'recursive' strategy.
     README.md | 1 +
     1 file changed, 1 insertion(+)

Great job, dope. Now look at your commit history:

    $ git log --oneline
    eabde5b Merge branch 'master' into foo
    f25c8a8 3. more more more
    21edb08 a. first commit
    31fd32d 2. adding
    0ad500b adding readme

We've mangled our commit history such that it looks like `21edb08` happened before `f25c8a8`, and that will hold true
when you inevitably merge this into master. Now things are out of order.

And even worse, you've introduced a dumb, useless merge commit `eabde5b`. 

Let's undo your mess:

    $ git reset --hard HEAD^1
    HEAD is now at 21edb08 a. first commit

    $ git log --oneline
    21edb08 a. first commit
    31fd32d 2. adding
    0ad500b adding readme

Now, instead of merging, let's rebase our branch on top of master:

    $ git rebase master
    First, rewinding head to replay your work on top of it...
    Applying: a. first commit

    $ git log --oneline
    76adf90 a. first commit
    f25c8a8 3. more more more
    31fd32d 2. adding
    0ad500b adding readme

Hey, look at that: our commit history is pretty. It's in the proper order that thigns happened, and we don't have a
meaningless merge commit.

### Rebasing

So what is rebasing? 

When you create a branch, you diverge from the mainline:

    A1 -> A2 -> A3
                   -> B1

`A1`, `A2`, and `A3` are commits to master. At `A3`, you create a new branch and make a commit, which becomes `B1`.

The base of your new branch is `A3`, as that's where you created it from. 

When further commits happen to master, you get behind:

    A1 -> A2 -> A3 -> A4 -> A5
                   -> B1 -> B2

When you rebase, you're moving the base of your branch forward. Git essentially re-runs your branch's commits on top of
master:


    A1 -> A2 -> A3 -> A4 -> A5
                               -> C1 -> C2

The base of the your branch is now `A5`, and your commits, `B1` and `B2` have been rewritten on top of `A5`. We call
them `C1` and `C2` as they are completely new commits with different changes and ref hashes.

To learn more about rebasing, with better graphics, checkout 
[Git Branching - Rebasing](http://git-scm.com/book/en/v2/Git-Branching-Rebasing).

### When to Rebase

So when does it make sense to rebase? Always? Definitely not. 

Rebasing makes the most sense when you're trying to incorporate others changes into your non-master branch. As part of
doing your work outside of master, you don't want to affect the order in which things happened in master, which merging
may do. You want to build your work on top of master.

It almost always makes sense to rebase your branch with master and never merge master into your branch.

This also gives the added benefit of solve conflicts in your branch before going to master, rather than solving 
conflicts in master.

### When to Merge

The inverse of this is that you should never rebase your branch into master, as it rewrites the history of master. 
Instead, your branch should always be merged into master.

There is a way to merge into master without generating a stinky merge commit, and that's to use the `--ff-only` option
when merging. Fast-forward only prevents a merge from happening, and will only pull your changes in if it can simply
move the branch `HEAD` pointer to the `HEAD` pointer of the branch you're merging it.

    git merge --ff-only foo

If it can't, it will fail. When it can't fast-forward, it's almost always because the branch you're trying to merge 
a branch that hasn't been rebased with master. If the base of the branch isn't the `HEAD` of master, it can't simply
drop your branch's commits on top of master.

To solve this, just make sure you rebase your branch with master:

    git rebase master
    git checkout master
    git merge --ff-only foo

This seems like a lot of work, but look how pretty and easy to follow your commit history is!

#### Dealing with GitHub

If you're using GitHub, or some other inferior system, you don't have control over pull requests are merged. GitHub
isn't going to do a rebase then a merge for you, they're just going to generate a merge commit. 

There is a way around this, but it's worse than the rebase-merge dance: you can handle the merge on the command line
and manually push up to master. 

Ain't nobody got time for that. At this point, it's probably easiest to just deal with the few merge commits generated
by pull requests. It's still less merge commits than those generated by merging master all the time.

### Beware of Rewriting History

Rebasing is essentially rewriting history, even if you end up with the same number of commits in the same order. 
Remember, rebasing generates brand new commits with new ref hashes. Why does this matter?

If anyone else happens to have those commits in their history -- say you push your branch up to GitHub so another
developer could work on it -- they're going to have a bad time. 

You're essentially destroying their history. The next time they try to pull updates from your branch, it's not going 
to work cleanly (unless they use a `git pull --rebase`, which they really should be doing anyway).

If they try to push up to your branch, they can't. Unless they force it, which at that point will erase your changes.
They'll first have to `git pull --rebase`. 

This just makes things more difficult. Workable, with proper communication, but difficult. 

Never should you rebase within master. If you do, you're asking for hellfire to rain upon you.

### Dealing with Conflicts

The downside to rebasing is conflicts. Conflicts can be generated when you merge or rebase, but they can become much
more difficult when rebasing. 

When merging with conflicts, you resolve all the conflicts at once, in one giant merge party commit. 

Remember that when we rebase, each commit is re-applied on top of master (or whatever branch you're rebasing onto). So
each commit being re-applied has the possibility of generating conflicts.

This means if you have 25 commits in your branch, and you're rebasing with master, that's 25 opportunities to generate
conflicts. You may have to fix similar conflicts several times as git rebases your changes.

This kind of sucks at first. Rebasin' ain't easy, but it feels right. And once you get used to it, you'll never want to
go back.
