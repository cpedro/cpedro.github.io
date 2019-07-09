---
title: "Git Hacks"
layout: default
---

Last Updated: 2019-03-13

Git is by the far the de-facto standard for version control, but out of box it
lacks a lot of tools that make it easier to work with.  I've built quite a
range of aliases and separate scripts to help with every day use.  Here's a
collection. of some of them.

## Use Most instead of Less or More

Not specifically Git related, but I would recommend using
[most](https://www.mankier.com/1/most) as your default pager instead of more or
less. It shows syntax highlighting, which is more than handy. You can install
it using the package manager of your distro if you're running Linux, or via
[Homebrew](https://brew.sh/) on macOS. Just install it, and set your `$PAGER`
environment variable to its path.
```
$ echo "export PAGER='/usr/local/bin/most'" >> ~/.bash_profile
$ export PAGER='/usr/local/bin/most'
$ echo $PAGER
/usr/local/bin/most
```

## Useful Git Aliases

Quick discard all changes on a file
```sh
$ git config --global alias.discard 'checkout --'
$ git discard <file>
```

Un-stage a file.
```sh
$ git config --global alias.unstage 'reset HEAD --'
$ git unstage <file>
```

Show last commit log
```sh
$ git config --global alias.last 'log -1 HEAD'
$ git last
```

Show all commits, in a nice format
```sh
$ git config --global alias.log-all 'log --oneline --decorate --graph --all'
$ git log-all
```

Show all commits, but more verbose that log-all.
```sh
$ git config --global alias.log-full 'log --pretty=medium --decorate --all --stat'
$ git log-full
```

## Turning On/Off GPG Signing
Like a good GitHub user, I always sign my commits.  However, there's a lot of
work repos that I don't need to / want to sign.  So I find myself turning on
and off GPG signing.  To help with this, I've written two Bash aliases that get
loaded in my `.bash_profile`.  That way, I can run `git-sign` to turn on
signing, and `git-nosign` to turn it off.

```sh
alias git-sign='git config --global commit.gpgsign true'
alias git-nosign='git config --global commit.gpgsign false'
```

## Git References Script
There's a number of repositories that have multiple refs, and it's a bit hard
to keep track of what state each is at.  I can run the `git log-all` or `git
log-full` from above, but I also wrote a Bash script that uses the for-each-ref
command to print them all out, with some basic information.

```sh
#!/bin/bash
# File: git-refs

printf "\e[1;34m${PWD}\e[0m"

fmt='
%(color:bold cyan)%(refname:short)%(color:reset):
  Commit:    %(color:yellow)%(objectname)%(color:reset)
  Date:      %(committerdate:format:%Y-%m-%d %H:%M:%S)
  Committer: %(committername) %(committeremail)'

git for-each-ref --sort=-committerdate --format="$fmt" refs
```

Sample output:
```
$ git-refs
/path/to/test
origin/master:
  Commit:    c1814782ddfcc5492ca9beff44801584313025b3
  Date:      2018-10-15 19:48:53
  Committer: Chris Pedro <chris@thepedros.com>

origin/HEAD:
  Commit:    c1814782ddfcc5492ca9beff44801584313025b3
  Date:      2018-10-15 19:48:53
  Committer: Chris Pedro <chris@thepedros.com>

GitLab/master:
  Commit:    c1814782ddfcc5492ca9beff44801584313025b3
  Date:      2018-10-15 19:48:53
  Committer: Chris Pedro <chris@thepedros.com>

GitHub/master:
  Commit:    c1814782ddfcc5492ca9beff44801584313025b3
  Date:      2018-10-15 19:48:53
  Committer: Chris Pedro <chris@thepedros.com>

master:
  Commit:    c1814782ddfcc5492ca9beff44801584313025b3
  Date:      2018-10-15 19:48:53
  Committer: Chris Pedro <chris@thepedros.com>
```

## Setting Up Multiple Remote Repos
Normally, you'll only have one remote repo, but this may not always be the
case. For example, I push all my personal public repos to GitHub and GitLab,
because why keep all your eggs in one basket?  I could just use `git remote`
commands to set up multiple remote repos, but I found it way easier to just
edit the `.git/config` file in the repo itself.  Doing this also allows you to
set two URLs for the 'origin' repo, which means anytime you push or pull to
'origin', you'll be pushing or pulling to or from both URLs.  This means, for
example, you simply do a `git push origin master` and have the repo synced up
to all remote repos in one go.

First, backup up your config in case anything goes wrong, then open
`.git/config` using your favourite text editor, and add all the remote repos
using the format below.  The `<REMOTE>` on the first and last line much match.
```
[remote "<REMOTE>"]
	url = <REMOTE_URL>
	fetch = +refs/heads/*:refs/remotes/<REMOTE>/*
```

Next, edit the `[remote "origin"]` directive and simply add the same `url =`
line you did above.  You can specify as many URLs as you wish all will be used
when doing a push.

As an example, below is the config I use for this projects repo.  As you can
see, I have a "GitHub" and "GitLab" remote repo named, and both URLs are under
"origin".  Each time I run `git push origin master`, both remote repos are
updated.  Also, I can pull or push to each individually by running `git pull
GitHub master` or `git pull GitLab master` for example.  Also, running `git
fetch --all` will run a 'fetch' on both remote repos.
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[remote "origin"]
	url = git@github.com:cpedro/cpedro.github.io.git
	url = git@gitlab.com:cpedro/cpedro.github.io.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
[remote "GitLab"]
	url = git@gitlab.com:cpedro/cpedro.github.io.git
	fetch = +refs/heads/*:refs/remotes/GitLab/*
[remote "GitHub"]
	url = git@github.com:cpedro/cpedro.github.io.git
	fetch = +refs/heads/*:refs/remotes/GitHub/*
```
