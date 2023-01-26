---
title: "How to Build and Launch ios Apps Faster"
date: 2023-01-07T15:02:27-05:00
draft: true
---

# Terminology

## Files
- `.c`: A source code file written in C programming language.
- `.o`: An intermediate object file. Also known as an object file. Made up from multiple `.c` files.
- `.a`: An archive file.  Made up from multiple `.o` files. Also known as a **Static Library**.
- `.dylib`: Linked with the app’s executable at runtime, but not copied into it. As a result, the executable is smaller and, because the code is loaded only when it is needed, the startup time is typically faster.
- ` ` : An executable. Note that the file type is empty. Executables usually don't have a file type. They're the end product, the program that gets ran/executed. Some examples of an executable are: 
    - iOS app: An app you open on your iPhone, ends up calling the app's executable. 
    - Static Library: The app's executable may also use an executable (library) to _build_ itself.  
    - Dynamic Library: The app's executable may also use an executable (library) to _launch_ itself. 
    - Other Examples - macOS binaries: Some examples that you use in the command line are: `ls`, `cp`, `mkdir`, `pwd` . Every time you do `ls`, you're calling a binary somewhere in your macos. Executables are also known as binaries. See [docs](http://www.linfo.org/bin.html) on `/bin`. Also see the `/bin` directory below:

!["/bin directory"](/binaries.png "If you do `which ls` then you'll get to see which binary is actually getting used")

## Tools:
- `cc`: Compiler
    - Converts a `.c` file to an executable. 
    - Converts multiple `.c` files into a `.o` file. 
- `ld`: Converts `.o` files into an executable. Also known as (static) linker. 
- `ar`: An archiving tool. Helps to bundle files together. Originally used for backups and distributions. Converts multiple `.o` files into a `.a` file
- `dyld`: A runtime linker, for dynamic linking. 

# Problem - A big final program

Suppose you need a single function from a library named `foo.a`. You end up consuming space for every other variable, function, type in library `foo`. 

What can you do resolve this?  

## Selective Loading

In a nutshell if you have the following code: 

!["Static Linking"](/static-linking-example.png)

Notice that the keyword `extern` means that the function is coming from an external file. 

### Scenario
- In main.c, there's a function called "main" that calls a function "foo". 
- In foo.c, there is foo which calls bar. In bar.c, there is the implementation of bar but also an implementation of another function which happens to be unused. 
- Lastly, in baz.c, there is a function baz which calls a function named undef. 

Now we compile each to its own .o file. 

- foo, bar, and undef don't have gray boxes because they are undefined [They're marked with `extern` i.e. they're not defined within the file that attempts to use them]. That is, a use of a symbol and not a definition. 

Now, let's say you decide to combine bar.o and baz.o into a static library. Next, you link the two .o files and the static library. Let's step through what actually happens.

### How linker works
First, the linker works through the files in command line order. The first it finds is main.o. It loads main.o and finds a definition for "main", shown here in the symbol table. But also finds that main has an undefined "foo". 

The linker then parses the next file on the command line which is foo.o. This file adds a definition of "foo". That means foo is no longer undefined. But loading foo.o also adds a new undefined symbol for "bar". 

Now that all the .o files on the command line have been loaded, the linker checks if there are any remaining undefined symbols. In this case "bar" remains undefined, so the linker starts looking at libraries on the command line to see if a library will satisfy that missing undefined symbol "bar". 

The linker finds that bar.o in the static library defines the symbol "bar". So the linker loads bar.o out of the archive. 

At that point there are no longer any undefined symbols, so the linker stops processing libraries. 

The linker moves on to its next phase, and assigns addresses to all the functions and data that will be in the program. Then it copies all the functions and data to the output file. Et voila! You have your output program.

# Questions

## Is linking and loading the same thing? 
- For Static Linking: Linking to a function is immediately followed by loading the function into the executable.
- For Dynamic Linking: You only link to the promise (interface of a function) during build. The loading of the function's implementation is delayed until the function is needed.

## What's the difference between a framework and a library?
- Library is a set of files
- Framework is a set of files bundle/packaged together — along with some other resources. Usually resources are things like storyboards, nibs, headers, localization files. However resources can also be a dynamic library or a static library. 


# References
[Mach-O Programming Topics](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/MachOTopics/1-Articles/building_files.html)


# NOTES

create a base branch off of main. `feature/xcf/base`
Find out the current Onboarding library that’s pointing to. 27.10.0

## Don’t Forget
log showbuildSettings
save the entire build log for apple developers to do an xcarchive
 The default optimization level for the Release configuration is Fastest, Smallest [-Os]

## XCFramework. No changes
create a branch for dh-gateway 
create a branch for its dh-ios
make an xcarchive
make the ipas
store it

## XCFramework. With changes
strip debug symbols. 
Think if there’s anything else to change
create a branch for dh-gateway 
create a branch for its dh-ios
make an xcarchive
make the ipas
store it

Other things to consider
we’re pulling in test files for our XCFramework. 

# References 

[Reducing your app’s size](https://developer.apple.com/documentation/xcode/reducing-your-app-s-size)

Before you start optimizing your app, you first need to measure its download and installation sizes. However, none of the binaries that you create for debugging or that you upload to the App Store from within Xcode are suitable for measuring your app’s size. For example, you can’t use any of the following binaries:
The APP file (the app bundle)
The XCARCHIVE bundle that you create when your archive your app
The IPA file that you upload to App Store Connect
Those binaries contain resources and files that aren’t part of the bundles that your users download from the App Store; for example, DSYM files for crash reporting.

Apps distributed for testing with TestFlight contain additional data that an App Store build doesn’t have, so the TestFlight build is larger. This additional data isn’t included in your app when you make it available in the App Store. However, when compared to the binary you uploaded, the final size of your app after it’s approved for the App Store may end up being slightly larger. This size increase can happen when the App Store performs additional processing on your app’s binaries, adding DRM to prevent app piracy and then re-compressing the binaries.

Automate the generation of the app size report
Instead of creating the app size report with Xcode, you may want to automate its generation in build scripts or continuous integration workflows. Run the following command to use xcodebuild to export your app for distribution and create an app thinning size report:

```sh
xcodebuild -exportArchive -archivePath iOSApp.xcarchive -exportPath Release/MyApp -exportOptionsPlist OptionsPlist.plist
```

About [the command](https://www.matrixprojects.net/p/xcodebuild-export-options-plist/)

👆 Best link 👆

https://www.runtastic.com/blog/en/frameworks-ios/

[Reduce the size of your app by stripping Swift symbols](https://help.apple.com/xcode/mac/current/#/dev6301c07c6)

Why do I want Swift symbols stripped from my app?
In most cases the symbols are not required for Swift frameworks including the Swift standard library. Stripping out the symbols can make apps significantly smaller.
How much space does symbol stripping save?
The amount of space you will save depends on a few factors. To find the size difference, build a distribution version of your app with and without the symbols stripped, install each of them on a device, and compare the size. You can build a distribution version for a registered device, for manual distribution, or for TestFlight. Looking at the size of an archive build is not a reliable way to measure the size of an iOS, watchOS or tvOS app because the Swift standard libraries are only stripped as part of App Thinning.

[What is an XCFramework?](https://help.apple.com/xcode/mac/current/#/dev6f6ac218b)
An XCFramework is a distributable binary package created by Xcode that contains variants of a framework or library so that it can be used on multiple platforms (iOS, macOS, tvOS, and watchOS), including Simulator builds. An XCFramework can be either static or dynamic and can include headers.

To use a prebuilt XCFramework, link the target to the XCFramework. Xcode ensures that the target can build against the XCFramework’s headers, link against its binary, and embed it for distribution. If your app has multiple targets (such as app extensions) that use the same XCFramework, you should pick one target (usually your app’s target) to embed the XCFramework and the others should link it without embedding.

[Create an XCFramework](https://help.apple.com/xcode/mac/current/#/dev544efab96)

[Monitor the status of builds](https://help.apple.com/xcode/mac/current/#/devb7cf06445)
EXCELLENT IMAGE

[object Object]
https://www.cocoanetics.com/2015/04/skipping-copy-phase-strip/