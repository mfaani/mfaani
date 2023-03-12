---
title: "Optimizing Binaries - high level build pipeline"
date: 2023-01-07T15:02:27-05:00
draft: true
category: "devtools"
tags: ["Xcode", "compilation", "linking"]
---

At the high level, the build process works like this: 
1. Dependency analyzing
> What happens when you press build? So the first step is for the build system to take the build description, your Xcode project file. Parse it, take into account all the files in your project, your targets and the dependency relationships. Your build settings, and turn it into a tree-like structure called a directed graph. And this represents all the dependencies between the input and output files in your project and the tasks that will be executed to process them. Next the low-level execution engine processes this graph, looks at the dependency specifications and figures out which tasks to execute. The sequence or order in which they must be run and which tasks can be run in parallel. Then proceeds to execute them.
From [WWDC 2022 - Behind the Scenes of the Xcode Build Process 4:57](https://developer.apple.com/videos/play/wwdc2018/415/?time=297)
2. Compile dependencies
- parse all files (swift, .m, .c, .cpp files) in module. 
- create intermediate files (object files) that the CPU can understand. 
3. Link all intermediate files
- Link all the intermediate files and create a binary
- Each dependency/module/framework/library needs to get built. So if you have 20 dependencies. Then you'll end up linking and creating 20 libraries. Also you'd have one app binary linked as well. So you have to link 21 times.
4. Copy resources and all binaries
- Each framework that's dynamically† linked will have to get copied into the app bundle. If you have 20 dependencies, then 20 frameworks need to get copied over into the app bundle. 
- Xcode creates the .app bundle directory. Then copies the dynamically linked frameworks over into your .app bundle. The copying is necessary because Xcode pulls the frameworks in a two step process: 
    1. All frameworks get created in the _build_ directory.
    2. Frameworks get copied into the final linked product.
It's like laying stuff on the floor, then adding a copy of each item into your backpack. Your backpack is the final linked product. 
5. Post Processing and Archiving 
    - stripping symbols (from each binary) and creating dSYMs.
    - code-signing
    - compressing the build
6. Distribution

†: Static libraries, don't have a separate binary in the final app bundle. Their code and symbols just get absorbed into app's main binary. More on that later. 

Throughout these series we'll start with small examples. Discuss the tooling that Apple has to compile and linke swift files. Then we'll discuss the difference of static vs. dynamic library and their impact on App Size. 
Then we'll segue into where/how the Xcode Build Settings affect the compilation of things. We'll further segue into CocoaPods and its faulty behavior. 

Lastly we'll discuss how to inspect an app bundle and its binary and get an app store thinning report. 