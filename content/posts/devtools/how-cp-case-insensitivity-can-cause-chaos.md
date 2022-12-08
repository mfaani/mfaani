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

Be sure to create a dummy folder if you wanted to test this out. Otherwise you could break an important directory in unrecoverable ways. SEE DISCLAIMER below before you proceed.


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

### DISCLAIMER: 
Once I did this, I wasn't able to easily delete the folder from the macOS bin. I was getting the follow error

!["macOS bin error"](/macOS-bin-error.png)

I found this [answer](https://apple.stackexchange.com/questions/412996/folder-wont-delete-from-trash-for-cant-delete-imageitems-because-its-pathn/435567#435567) that helped me figure out. I ultimately needed to do: 

```bash
sudo rm -rfv <path_of_folder_to_delete>
```

## Why does this happen? 
It happens because I didn't eat my vegetables. But also because:  
Effectively `cp -r blog BLOG` is like doing `cp -r blog blog`. 
As if when you're copying, the `cp -r` command sees new updates to what it just copied. It end up **recursively** copying the `blog` folder back into itself. By every new copy, the next paste it does gets bigger and bigger. 🤷

This made the source controlled project to have a directory with an identical name to the root directory. Each nested directory was also version controlled. 

At the end I just ended up re-cloning my project. Only had a day of some unpushed commits, but that's the price I was willing to pay. 

Additionally even with the finder, I was unable to create two folders named `Blog` and `blog`. It's because the [Apple File System (APFS)](https://support.apple.com/guide/disk-utility/file-system-formats-dsku19ed921c/mac) is case-insensitive by default. The APFS formats are: 

- APFS: Uses the APFS format. Choose this option if you don’t need an encrypted or case-sensitive format. (The default)
- APFS (Encrypted): Uses the APFS format and encrypts the volume.
- APFS (Case-sensitive): Uses the APFS format and is case-sensitive to file and folder names. For example, folders named “Homework” and “HOMEWORK” are two different folders.
- APFS (Case-sensitive, Encrypted): Uses the APFS format, is case-sensitive to file and folder names, and encrypts the volume. For example, folders named “Homework” and “HOMEWORK” are two different folders.

You can set the format from Disk Utility like this: 

!["APFS Types"](/add-apfs-volume.png)

Then if you select a volume, it will show up like this.

!["APFS"](/APFS.jpg "Shows the encrypted and case-sensitive formats. You can add/view from 'Disk Utility'")

All of this means if you have a directory that you need opening then doing `cd Blog` or `cd blog` makes no difference. The auto-complete from ZSH treats them the same.

## What's the solution? 
Either: 
- Use `mv` instead to move. It will do the right thing.
- Use `cp -r`. However give it a different name. And then manually change the name yourself. 
- Change your APFS to be case-sensitive. 
