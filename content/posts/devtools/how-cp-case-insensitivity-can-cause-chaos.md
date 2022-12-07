---
title: "How cp case-insensitivity can cause chaos!"
date: 2022-12-06T21:20:30-05:00
category: "devtools"
description: "How cp ends up being dangerous...!"
tags: ["mac-os", "cp", "git", "case-sensitivity of cp"]

editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

Been doing [Advent of Code](https://adventofcode.com) in the last couple of days. I completed Day 6. I have been doing it from an Xcode project. However I was thinking it would be nice if I could do it from command line. So I started to do some code clean up. First I started with renaming the folder from `AOC` to `aoc`. I did: 

```bash
cp -r AOC aoc
```

I saw a new `aoc` directory created. I was happy. It was easy. Or at least it seemed easy.  
I went back to my `AOC` directory. I noticed a whole bunch of changes that I didn't make. Confused! I tried figuring it out. I couldn't.  
Decided to remove the changes with:

```
git checkout .
git clean -fd
```

I ran `git status`. Everything was good. Then I went back to my project. I re-created `Day6` folder. Xcode through me an error.

!["File Already Exists"](/xcode-path-already-exists.png)

I ran `git status` again. Didn't see any folder named Day6 added. From the project's directory I did `ls AOC`. In the output I saw another `AOC` folder.  
I did `ls AOC` again. I saw `AOC` included again. It seemed that I somehow had managed to created a semi-infinite number of AOC folders. But how did I?

**It's because `cp` is case-insensitive**

## How can I test this out?

Be sure to create a dummy folder if you wanted to test this out. Otherwise you could break an important directory in unrecoverable ways.

Here's how you can test it. Just do: 

```bash
mkdir blog
cd blog
touch post1
touch post2
cd ..
cp -r blog BLOG # notice the different cases
```

The output I got is: 

!["cp hell"](/cp-hell.png)

## Why does this happen? 
It happens because I didn't eat my vegetables. But also because:  
Effectively `cp -r blog BLOG` is like doing `cp -r blog blog`. 
As if when you're copying, the `cp -r` command sees new updates to what it just copied. It end up **recursively** copying the `blog` folder again. By every new copy, the next paste it does gets bigger and bigger. 🤷

This made the source controlled project to have a directory with an identical name to the root directory. Each nested directory was also version controlled. 

At the end I just ended up re-cloning my project. Only had a day of some unpushed commits, but that's the price I was willing to pay. 
