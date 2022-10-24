---
title: "Why does pod lib lint suddenly fail to build?"
date: 2022-10-24T10:57:20-04:00
description: "Why might pod lib lint fail to build while the app itself builds fine?"
category: "devtools"
tags: ["cocoapods", "CI", "PodSpec", "linting", "lock file"]
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

I made some changes to the repo of our private pod. Pushed up my branch. Tests all ran. Then my colleague asked for some updates to my PR (pull request). I made changes and pushed it to GH. I wanted to merge the changes. But then CI was failing. So I ran CI again. It failed again. They say third time is the charm. So I ran it two more times. It still failed. I thought I had changed something in the project file, because I was getting some Swift compatibility error. I erased the derived data and clean build. I was able to build and run all tests. I wasn't sure why things were still failing. 

I tried creating a new dummy PR with just an empty new file. It still failed. I then thought it was perhaps something in the last commit. It wasn't. 

## Root Cause
Problem was from an incompatible dependency. But how did it show up all of a sudden out of no where?!

The `PodSpec` was doing something like:

```ruby
s.dependency 'FoodFire',  '~> 4.0'
```
Note: The`Podfile` didn't have any mention of `FoodFire`. It just relied on the `PodSpec` for its dependencies. 

A single CI run does two things: 
- Build, compile the project. Go through its unit-tests and UI-tests. 
- Do `pod lib lint --private --allow-warnings --verbose --fail-fast --skip-test`


So lets say the last time you did `pod update FoodFire` the latest release of `FoodFire` was `4.1.3`. This will write the following into your `Podfile.lock`: 


```Ruby
PODS:
  - APIClient (1.4.0)
  - FoodFire (4.1.3): # This is the restriction you ultimately end up with. 
    - Trucks (~> 7.7.1)
  - MyCoolPod (24.0.0): # THIS IS OUR POD
    - SmoothManager (= 2.0.1)
    - APIClient (~> 1)
    - SwiftyGif (= 9.0.1)
    - FoodFire (~> 4.0) # This the restriction set on the version. 
  - SmoothManager (2.0.1)
  - SwiftyGif (4.2.1)
  - Trucks (7.7.1)
```

In your CI, you run the Example app and all its unit-tests + UI-tests. Great. Everything works. 

Then you do `pod lib lint` and all of sudden things go to hell 🤬.

The following will explain how that can happen. 
1. `Podfile.lock` is using `FoodFire` 4.1.3
2. An engineer goes in and publishes new 4.1.4 `FoodFire` PodSpec. 
3. Your CI then goes through its linting process: 
    1. run `pod repo update` so your agent has all the latest resources it needs
    2. run `pod lib lint`

What you have to understand is that:
- At step 3.1, your agent has now downloaded the 4.1.4 PodSpec. 
- Your linting is rightfully oblivious to `pod lib lint`. It will use 4.1.4 (not 4.1.3)

At this moment your linting vs. normal project building/running have bifurcated into using two different sets of dependencies.  
In my situation, the 4.1.4 had some buggy code. It was causing linting to fail. 

### Debugging tips to resolve such issues faster?
1. In general if things only fail on CI, then it's usually a good idea to go step by step with exactly what your CI is doing.
2. Run `pod lib lint` locally and see if things work. If doesn't then look into the logs for anomaly. See if all the dependency is using the version you're expecting. 
3. Pay closer attention to the version thats logged in CI. See if it's the version you expect.
4. Run the app locally with the version CI gives and see if things work as expected. 

## Summary

`pod lib lint` is oblivious to `Podfile` & `Podfile.lock`. It only cares about the versions mentioned in the `PodSpec`.  
For this reason, if you're using the [optimistic operator](https://stackoverflow.com/questions/20213751/what-is-the-usage-of-in-cocoapods) then the outcome of 
```ruby
pod repo update
pod lib lint
```
will vary depending on the time it was ran.