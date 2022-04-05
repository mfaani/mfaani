---
title: "How to cherry-pick a library fix?"
date: 2022-04-05T12:22:16-04:00
categories: [Devtools]
tags: [git, cherry-pick, library]
cover:
    image: "git-cherry-pick.jpg"
    alt: "git cherry-pick"
    relative: true
---

I've done this so many times, but every time I do it with hesitance. 
It's usually because I'm not certain I'm finding the right commit or if I'm applying it the right way. 
These steps explain the process and how to validate that you're doing thins the right way. 

1. Find out the library version that your release is using.
2. Check out that version of your library.
3. Create a new branch off that version.
4. Find the SHA of the commits you want to cherry pick. 
5. Cherry pick them in and validate you've cherr-picked correctly.
6. Make a new release/tag.
7. Go back to your main app. Check out the release branch again. Create a new release version
8. Update your `Podfile` (or whatever it is that you're using) to pull the updated version.

All the steps together

```
# 1.1 
cd path/to/mainProject 
# 1.2 
git checkout release/22.0 # You see that your `Podfile.lock` is using the `1.32.0` version of you library

# 2.1 
cd path/to/library 
# 2.2 
git checkout 1.32.0 

# 3
git checkout -b release/1.32.1 # (IMPORTANT. Avoid creating a branch named `1.32.1` because you git won't know if you want to checkout the branch or tag `1.32.1`)

# 4.1 
git checkout main 
# 4.2
git log --oneline -20 # copy the SHA of the commit. I only added `--oneline` because I wanted it to be in one line. `-20` means last 20 commits. 

# 5.1 (Optional)
git show <SHA of the commit I wanna cherry pick> # If I wanted to be sure that I'm cherry picking things correct, then I just do  and I can see what exactly I'm cherry picking. I highly recommend doing this.
# 5.2
git cherry-pick aec4e0f9; git cherry-pick d963d6bc  # (Cherry-picking two commits)
# 5.3 (Optional)
git diff 1.32.0 # validate all the changes version your previous library release.

# 6
git tag 1.32.1 

# 7
cd path/to/mainProject; git checkout release/22.0; git checkout -b release/22.0.1 

# 8
Update your `Podfile` or whatever dependency management system you're using to use `1.32.1` of your library and pull in the changes with `pod install`.
```