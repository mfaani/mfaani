---
title: "Optimizing Binaries - Jargon"
date: 2023-01-07T15:03:27-05:00
draft: true
---

To understand this post, I highly recommend everyone watches the [WWDC 2022 - Link fast: Improve build and launch times](https://developer.apple.com/videos/play/wwdc2022/110362) and [WWDC 2018 - Behind the Scenes of the Xcode Build Process](https://developer.apple.com/videos/play/wwdc2018/415/). They're of the best talks I've seen. 

Some jargon and history I learned from watching: 

## Files
- `.c`: A source code file written in C programming language.
- `.swift`: A source code file written in Swift programming language.
- `.o`: An intermediate object file. Also known as an object file.
- `.a`: An archive file. Made up from multiple `.o` files. Also known as a **Static Library** or **Archive**.
- `.dylib`: Linked with the app’s executable at runtime, but not copied into it. As a result, the executable is smaller and, because the code is loaded only when it is needed, the startup time is typically faster. Also known as **Dynamic Library**.
- ` ` : (empty file type). A binary. An executable. Executables usually don't have a file type. They're the end product, the program that gets ran/executed. 
As the name suggests it's just 0s and 1s. It's the lowest possible language level. You can't get any lower than that. Some examples of an executable are: 
    - An iOS app's main executable: An app you open on your iPhone, ends up calling the app's executable. Example the Safari app on your iPhone has a `Safari` executable. 
    - Other Examples - macOS binaries: Some examples that you use in the command line are: `ls`, `cp`, `mkdir`, `pwd` . Every time you do `ls`, you're calling a binary somewhere in your macos. Executables are also known as binaries. See [docs](http://www.linfo.org/bin.html) on `/bin`. Also see the `/bin` directory below:

!["/bin directory"](images/binaries.png "If you do `which ls` then you'll get to see the path to the binary that is used")

## Tools:
- `cc`: Compiler
    - Converts a `.c` file to an executable. 
    - Converts multiple `.c` files into a `.o` file. 
    - Can't be used to compile swift files. Must use `swiftc` instead.
- `ld`: Converts `.o` files into an executable. Also known as the (static) linker. This is a very important part of the whole build process.
- `ar`: An archiving tool. Helps to combine files together. Originally used for backups and distributions. Converts multiple `.o` files into a `.a` file. The difference between `ar` and `ld` is that `ld` creates an executable, whereas `ar` just groups object files together. The linker was later enhanced to know how to read .o files from an archive file. 
- `dyld`: A runtime linker, for dynamic linking. 

All the above tools are universal and exist outside the Apple world. The following tools are Apple specific. 

- `clang`: Apple's compiler for 'C language family': C, C++, Objective-C, Objective-C++. 
- `swiftc`: Is the command-line interface to the Swift compiler, which is responsible for compiling Swift source code into machine code that can be run on a computer. It basically does what `cc` does for `.c` files, but just for `.swift` files. It's ultimately a symlink to `swift`. For more on that see [here](https://stackoverflow.com/questions/57777091/whats-the-difference-between-swift-and-swiftc)

### How do all the tools and files work together?
1. All source files are compiled into an intermediate file known as object code/file (.o file)
2. Any needed object file will get linked together. Often you link source code and libraries together. Certain tools are used to create libraries. 

The compiler creates an object file. But a file by itself is useless by itself. They're like a piece of a puzzle. 
The linker links all necessary object files and creates the final binary. 


## Xcode Jargon

### Dependency order 

Build Process Represented as a Directed Graph

> What happens when you press build? So the first step is for the build system to take the build description, your Xcode project file. Parse it, take into account all the files in your project, your targets and the dependency relationships. Your build settings, and turn it into a tree-like structure called a directed graph

The order: 
- Knowledge built into the system
- Target Dependencies
- Implicit Dependencies
- Build Phase Dependencies
- Scheme order Dependencies 

### Incremental Build

And of course the bigger your project, the longer the build process will take. So you don't want to run all of these tasks every single time you build.
Instead, the build system might only execute a subset of the tasks on the graph. Depending on the changes you've made to your project since the previous build.
We refer to this as an incremental build and having accurate dependency information is very important in order for incremental builds to work correctly and efficiently. Now we talked about how changes affect the build system, and how they relate to incremental builds. So how does the build system actually detect changes? Each task in the build process has an associate signature which is the sort of hash that's computed from various information related to that task.
This information includes the stat of the task's inputs like file paths and modification time stamps. The command line indication used to actually perform the command. And other task-specific metadata such as the version of the compiler that's being used.
The build system keeps track of the signatures of tasks in both the current and the previous build. So that it knows whether to rerun a task each time a build is performed.

### Embedded Binaries 

Embed, means to add a copy of that framework into your application bundle under the `/Frameworks` directory by default. Your application won’t be able to do anything with that Framework unless it links to it. Xcode takes the liberty of doing this for you. (System frameworks are already present on either iOS or macOS so you can safely link to these at the absolute path that they are kept on the system. Your own custom frameworks won’t be present on a user’s system, and therefore they have to be embedded in the application bundle.)
Linking and embedding indirectly imply dynamic and/or static linking. We now know that embedding wouldn’t make any sense for a static library because the symbols from the static library are compiled into the executable, so Xcode won’t let you drop a static library under the Embed section. It will however, let you embed a framework with static libraries. This is an inefficient use of frameworks.
from [Big Nerd Ranch](https://bignerdranch.com/blog/it-looks-like-you-are-trying-to-use-a-framework/)

# Problem - A big final program

Suppose you need a single function from a library named `foo.a`. You end up consuming space for every other variable, function, type in library `foo`. 

What can you do resolve this? See the next post on [How does the linker help reduce app size? What are the different linker types?]() @self todo: "add link"