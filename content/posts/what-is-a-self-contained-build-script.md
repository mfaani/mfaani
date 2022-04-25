---
title: "What is a Self Contained Build Script?"
date: 2022-04-22T13:11:12-04:00
categories: [Devtools]
tags: [CI, jenkins, bundler]
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
cover:
    image: "self-contained-rv.jpg"
    alt: "self-contained rv"
    relative: false
---

So I had this need to add a brew package named `gh` into our build script. I spoke with with the team that handled our agents and asked them to add a new package on the agents. 

I was told that the package has to be included as part of the build script and that build scripts need to be self-contained. 

At first I didn't fully understand what 'self-contained' means in this context but as I digged more into our `Jenkinsfile` I learned what it meant. First let's figure out:

### How is running `pod lib lint` on my local machine different from running it on an agent?
On my local machine if I want to run `pod lib lint`, I just do it. I don't ever check if CocoaPods is installed or not. I just know that I've installed and use it. 

However you can't assume the same on a agent. An agent isn't owned by you. And you have no control over what command line tools it has. 
You need `CocoaPods` installed. 

- To have `CocoaPods` you need (recommended) `bundler`.
- To have `bundler` you need `ruby`.
- To have `ruby` you need (recommended) `rvm`.

Our CI team installs `rvm` on all agents, as that's not a trivial process. The rest (installing ruby, bundler, CocoaPods) is to be owned by the build script.

### Hmmm. OK. So should I install ruby, bundler, cocoapods on every build? 
No. You just install it if it wasn't installed before. Example: 

```bash {linenos=true linenostart=1}
rvm use ruby-2.5.1 || rvm install ruby-2.5.1
gem install bundler -v "2.3.3" 
bundle install
pod lib lint
```
Line 1 only installs ruby if it's not installed.  
Lines 2 & 3 only install packages once. The `bundle` and `gem` commands have have logic internally to exit early if the dependencies are installed already.  
Line 4 can then safely execute.

### What should I do if I need a brew dependency installed?
- On all agents: Have `brew` installed. This needs to be done only once. 
- In your build script do: `gh --version || brew install gh`. 

By having `gh --version || brew install gh` in your build script, you make your scripts **self-contained** i.e. your build script doesn't depend on the `gh` formula being installed. 

### And what's the cover image about? 

A self-contained RV is an RV that has a bathroom and tank system for holding water. It doesn't need any outside sources to operate.

Build scripts need to be just like that. 😀  
They should be able to work without knowing what dependencies exist on the agent. 

### Conclusion

It's ok to expect `rvm`, `brew`, `Xcode` to be installed. But for any other dependency or package, you should check for its presence. If not available then install it during your build script.