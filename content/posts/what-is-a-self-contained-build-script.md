---
title: "What is a Self Contained Build Script?"
date: 2022-04-22T13:11:12-04:00
categories: [Devtools]
tags: [CI, jenkins, bundler, build-scripts, keychain]
description: "Deep dive into how certificates are retrieved and common problem one can face for retrieving certificates for signing"
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

### Anything else? 
💡
Yes. A good RV won't leave any trash behind itself. Similarly build scripts must clean up after themselves. Usually that's done with just deleting after your checked in the branch. However things can quickly get tricky if you make changes **outside** of your repo's _directory_. 

Example: if you create a new keychain with the name of "FooProject" and don't delete it once your build script finishes, then if the next build script attempts to [create a keychain](https://jonlabelle.com/snippets/view/shell/security-command) with the same name then it will fail. 
#### Creating same Keychain twice
```
$ security create-keychain -p kevin123 FooProject.keychain-db # (ran by build job 1... no error)
$ security create-keychain -p kevin123 FooProject.keychain-db # (ran by build job 2)
security: SecKeychainCreate FooProject.keychain-db: A keychain with the same name already exists. 
```

Looking into the logs makes it easy for you triage. But if it's something that leads to a visual error or a visual password prompt then it's HARD to triage. You may not have access to the build machine and would need to pair with the team that owns the machines and SSH into it and triage things together. Example such a prompt would pause the build while leaving no trace on your machine. Only way to know it happened is to SSH into the agent.

![keychain-prompt](/keychain-prompt.png)

You have two ways to resolve this:

1. At the end of your build script, clean up even things you did outside your checked out directory. 
2. At the beginning of your build script, make sure things are clean i.e. delete any keychain that could cause conflicts for your build script.

The first solution works if the build agents only use a single build script. If multiple teams (and more in the future) are using the same agents, then it becomes hard to track all their build scripts / GitHub Actions / Fastlane, etc and see if they do proper clean up. 

The second solution works better. Because it does a base clean up within itself — before starting. Just remember to log and report back to the owners of the faulty build scripts. 

### Conclusion

It's ok to expect `rvm`, `brew`, `Xcode` to be installed. But for any other dependency or package, you should check for its presence. If not available then install it during your build script. 

If you touched anything outside your working directory, then it's best to clean up afterwards. You also can't assume other build scripts have done proper clean up, so you may need to do a base clean up _before_ doing anything in your script. 