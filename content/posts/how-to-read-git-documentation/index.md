---
title: "How to Read Git Documentation"
date: 2022-02-11T08:55:15-05:00
category: [Devtools]
tags: ['git', 'Documentation', 'Source Control']
description: "I was always perplexed with gits documentation. You need another documentation to just understand it. In this post I give tips on how to decipher it."
cover: 
    image: "images/git-branch-synopsis.png"
    alt: "git branch synopsis"
    relative: true
---

I used to find it super hard to read git syntax. I didn't know what what `--`, `<>`, `[]`, `...`, `|`, `()`, etc meant. 
Turns out, it's a whole lot easier than you think. 

The synopsis contains a list of most likely _forms_ you'd try. But sometimes the synopsis is just one big long synopsis where all the options are thrown in at once. This can make it hard to understand the different ways of using the options and commands. 

## Syntax Guide:

- `[ ]` makes things optional.
- `< >` are just placeholders. 
- `...` just means one or more. 
- `|` means: OR
- `( )` just groups things together for easier readings.
- `-b`, `-d`, `--oneline`, `--add` and so on are known as options

Beyond that, if you scroll down, you'd see each page usually (but not always) has the following sections, structure. 

## Sections

- Name
- Description
- Options:
- Commands
- Examples
- Notes 
- See Also  

Within the Options sections, the _placeholders_ are better described. Example: the following placeholders are extracted from [git checkout](https://git-scm.com/docs/git-checkout) page:

![git checkout placeholders](images/git-checkout-placeholders.png "git checkout placeholders. These are within the Options sections.")

With this knowledge let's go through some examples: 

### First Example
```bash
git branch (-d | -D) [-r] <branchname>…​
```
Translates to:

- `git branch`
- either `-d` or `-D`
- optional `-r`
- one or more branch names

Meaning, all of the following are accepted: 

```bash
git branch -d branchA
git branch -d branchA branchB branchX
git branch -d -r branchA branchP
```

While the following aren't accepted:

```bash
git branch -d -D branch # can't provide both -d -D together
git branch -d # must provide at least one branch name
```

### Second Example:  
```bash
git remote set-branches [--add] <name> <branch>…​
```

Translates to: 
- `git remote set-branches`
- optional `--add`
- must include name (of the repo)
- one or more branch names

Meaning, all of the following are accepted: 

```bash
git remote set-branches --add origin branchA
git remote set-branches origin branchA
git remote set-branches origin branchA branchB
git remote set-branches --add origin branchA branchB <a lot more branches> branchX
```

While the following aren't accepted:

```bash
git remote set-branches --add origin upstream branchA # you can't do multiple repos. Only multiple branches
git remote set-branches --add origin # you must specify a branch name
```

Now you might be asking what does `<name>` refer to. The answer is, you have to go into the command's page and see the description of the command you're using. E.g. the description of: `set-branch` can be found from [here](https://git-scm.com/docs/git-remote#Documentation/git-remote.txt-emset-branchesem) 

> Changes the list of branches tracked by the named remote. This can be used to track a subset of the available remote branches after the initial setup for a remote.
> The named branches will be interpreted as if specified with the -t option on the git remote add command line.
> With `--add`, instead of replacing the list of currently tracked branches, adds to that list.


### Third Example: 

```bash
git diff [<options>] [<commit>] [--] [<path>…​]
```

Translates to: 
- `git diff`
- Any number of options. This is an odd one, because it's not marked with `...`, but the placeholder itself conveys that there can be multiple options. Typically options can be multiple.
- a commit (SHA)
- `--` is just a separator added at the end of options. You don't need need it unless git needs it to disambiguate your command. 
- zero or more paths.

Meaning, all of the following are accepted: 

```bash
git diff . # diff on the current directory
git --name-only 97c8fa32a some/directory # shows the file name of every thing changed (from only inside some/directory) between the current index and the `97c8fa32a` commit 
```

### Forth Example

```bash
git diff [<options>] <commit>…​<commit> [--] [<path>…​]
```

So now you saw this and didn't know what `<commit>…​<commit>` means. It's easier to figure out, because we know what the other parts of this form mean. All we have to do is, search for it within the [docs of git diff](https://git-scm.com/docs/git-diff). If we do see we find:

> This form is to view the changes on the branch containing and up to the second `<commit>`, starting at a common ancestor of both `<commit>`. [skipping some parts of doc] .You can omit any one of <commit>, which has the same effect as using HEAD instead.

Meaning the following is allowed: 

```bash
git diff --name-only 5a189bf...e48224a
git diff --name-only 5a189bf...e48224a -- some/directory
git diff --name-only 5a189bf...e48224a -- some/directory1 some/directory2
```

💡💡💡 Surprisingly the followings are **also allowed**:

```bash
git diff --name-only 887bbeab6...3.29.0.1616171962 
git diff --name-only 887bbeab6...branch32A
```

The 👆 are allowed, because both a tag and branch point to a specific commit, so they both satisfy as a `<commit>`. 

### Fifth Example

```
git log [<options>] [<revision-range>] [[--] <path>...]
```
Translates to: 
- `git log`
- Any number of options. This is an odd one, because it's not marked with `...`, but the placeholder itself conveys that there can be multiple options. Typically options can be multiple.
- revision-range: Git allows you to refer to a single commit, set of commits, or range of commits in a number of ways. They aren’t necessarily obvious but are helpful to know. For more on that see [here](https://git-scm.com/docs/git-log#Documentation/git-log.txt-ltrevision-rangegt) and [here](https://git-scm.com/book/en/v2/Git-Tools-Revision-Selection). When no `<revision-range>` is specified, it defaults to `HEAD` (i.e. the whole history leading to the current commit). `origin..HEAD` specifies all the commits reachable from the current commit (i.e. `HEAD`), but not from `origin`. 
- `--` is just a separator added — if you want to specify one or more paths. You don't need need it unless git needs it to disambiguate your command. Zero or more paths.

Meaning the following is allowed: 

```bash
git log
git log -10
git log cj0ds9232..adfs923kcv 
git log -S "plus sign" -- Documentation/git-branch.txt
```
It bit explanation for the last item:
The [`-S`](https://git-scm.com/docs/git-log#Documentation/git-log.txt--Sltstringgt): 

> It is useful when you’re looking for an exact block of code (like a struct), and want to know the history of that block since it first came into being

Ultimately what this does is, searches in all your commits at the given path and tries to find mentions of "plus sign" in the contents of your commit. Hat tip to user Torek. See [here](https://stackoverflow.com/questions/73486442/why-does-git-branch-annotate-a-branch-with-and-another-with/73486443#comment129808004_73486443)

## Other notes:

- To familiarize yourself more with jargon used in the pages. See [git glossary](https://git-scm.com/docs/gitglossary) 📖. 
- Understanding what the placeholder is about is key. Take your time and search on the doc page (or on the internet) for it. Common placeholders are: 
    - `<path>`
    - `<pathspec>`
    - `<branch>`
    - `<repo-name>`
    - `<tree-ish>`
    - `<commit>`

- Options and commands are different. 
    - Options are prefixed with `-` or `--`. 
    - Comamnds don't have any `-` or `--` before them. `git add`, `git log`, `git diff`, `git remote`, etc are all different commands. 
    - Some commands have sub-commands e.g. `git remote` has different commands:
        - `git remote add`
        - `git remote remove`
        - `git remote rename`
- Every git command has various options. If you don't set them, then git will fallback to its defaults. A lot of times the current commit, branch, directory get used if you dont' specify antything. 

- The position of `...` is important. Example:  `<branch>...` is different from `[<branch>...]`
    - `<branch>...` means one or more branches.
    - `[<branch>...]` means optionally  1 or more branches. i.e. zero or more branches.


- The git doc pages are not identically structured. If you can't understand the docs, it's not your fault. It's because the documentation is written by lots of different people. 

## Also see:

[How do I read git synopsis documentation?](https://stackoverflow.com/questions/60906410/how-do-i-read-git-synopsis-documentation/60906506#60906506)
