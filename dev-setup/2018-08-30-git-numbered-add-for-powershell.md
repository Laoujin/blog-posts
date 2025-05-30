---
layout: post
author: Wouter Van Schandevijl
title:  "Git-NumberedAdd for PowerShell"
date:   2018-08-30 15:00:00
desc: >
  Tired of copying <code>git status</code> output for <code>git add</code> input for the millionth time?
  Despair no more for <code>Git-NumberedAdd</code> is here!
img:
  url: git-add-numbered-add.jpg
categories: dev-setup
tags: [git,powershell,product]
extras:
  - githubproject: https://github.com/itenium-be/Git-NumberedAdd
    githubtext: Git-NumberedStatus, Add, Diff, etc ps1 source
interesting:
  - desc: Similar implementation in Bash
    git: scmbreeze/scm_breeze
  - desc: Similar implementation in Perl
    git: holygeek/git-number
toc:
  title: Numbered Add
  icon: icon-git
lastUpdate: 2019-01-12 00:00:00 +0200
updates:
  - date: 2019-01-12 00:00:00 +0200
    desc: More actions and utilities + bugfixes
redirect_from: /blog/productivity/git-numbered-add-for-powershell/
---

After copying file paths from `git status` output and pasting them after a `git add` quite a few times
by now, I've written a small [PowerShell script](https://github.com/itenium-be/Git-NumberedAdd)
to manipulate the working directory and staging area with fabricated indexes.

<!--more-->

# Git-NumberedHelp <small>(alias: gnh)</small>

All these aliases to save time but who can remember all that?  
Use `gnh` to display all actions available by Git-NumberedAdd with description and alias.


# Git-NumberedStatus <small>(alias: gs)</small>

Displays the output of `git status --short` together with fabricated indexes like so

![Git-NumberedStatus](/assets/blog-images/git-add-numbered-status.png){: .img-responsive}

Working directory color codes:
- Yellow: <span style="color: #CDCB7D">**M**</span>odified (file0 to file3)
- DarkMagenta: <span style="color: #8C5998">**D**</span>eleted (file4)
- Blue: <span style="color: #0480EF">**A**</span>dded (file5)

Git-NumberedStatus accepts extra CLI arguments. Example: `gs -u` to see all `--untracked-files`.  
Configure the color output by modifying `$global:gitStatusNumbers`.

## With Numstat

If `$global:gitStatusNumbers.includeNumstat` is true (=by default), Git-NumberedStatus will also
execute a `git diff --numstat` and add lines added/deleted to the output.

![Git-NumberedStatus with --numstat](/assets/blog-images/git-add-numbered-status-numstat.png){: .img-responsive}


# Git-NumberedAdd <small>(alias: ga)</small>

All set to stage some files!

```ps
PS> Git-NumberedAdd 0 1 2
add 'file0'
add 'file1'
add 'file2'
```

The same can be achieved with:  
- `Git-NumberedAdd 0-2`
- `Git-NumberedAdd -3`
- `Git-NumberedAdd 012` (but only if there is a max of 10 files in the git status)

`Git-NumberedAdd +3` would add file4 and file5 in the above scenario.



# Git-NumberedDiff <small>(alias: gd)</small>

Works like `Git-NumberedAdd` but can also be called without arguments to
see the diff of all files. It will call `git add -N` for all newly created
files so that they also show up in the diff.

`git diff --cached` is currently not implemented in this script.

# Git-NumberedReset <small>(alias: grs)</small>

This only works for files already staged.
Example output:  

![Git-NumberedStatus](/assets/blog-images/git-add-numbered-status-staged.png){: .img-responsive}

To, for example unstage file0 and file4  
```ps
PS> Git-NumberedReset 02
Unstaged changes after reset:
M       file0
M       file1
M       file2
D       file4
```

# All actions

Wrappers for all your basic git commands

```powershell
# Manipulate working directory:
Git-NumberedAdd # Alias: ga
Git-NumberedAddPatch # Alias: gap
Git-NumberedDiff # Alias: gd
Git-NumberedDiffCached # Alias: gdc
Git-NumberedCheckout # Alias: gco

# Manipulate the staging area:
Git-NumberedReset # Alias: grs
```

## Git-Assuming

Unfamiliar with "assume unchanged"?
Check the docs on [`git update-index --assume-unchanged`](https://git-scm.com/docs/git-update-index).

After a `Git-NumberedStatus`, assume a file in the working directory with `Git-NumberedAssumed` (alias: gas)

Unassume files later on:

```powershell
# List all currently assumed files
Git-ListAssumed # alias: gasl

# and follow with:
# git update-index --no-assume-unchanged
Git-NumberedUnassumed # alias: gnoas
```


## Utilities

```powershell
# Get the full path of a file
Git-GetFileNameByIndex 2 # alias: gn

# Delete file with index 2
rm (gn 2)


# Change current path to single index location
Git-NumberedSetLocation # Alias: gsl
# Use Pop-Location (alias: popd) to go back to where you came from
```

# Alternatives

Sometime similar is actually built into Git itself
```bash
git add --interactive # or -i for short
```

Once in interactive mode:  

![git add --interactive](/assets/blog-images/git-add-interactive.png){: .img-responsive}

And then use `2: Update` to stage files.

<br>
Also, see below for similar implementations in Bash and Perl.
