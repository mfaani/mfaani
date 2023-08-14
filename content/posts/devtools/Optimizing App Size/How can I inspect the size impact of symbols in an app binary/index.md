---
title: "How Can I Inspect the Size Impact of Symbols in an App Binary: A Practical Guide for Apple Developers"
date: 2023-08-11T16:21:06-04:00
category: "Devtools"
tags: [nm, symbols, App Size, iOS, macOS, swiftc, dSYM, strip]
ShowToc: true
description: "Learn how to use nm to count extra symbols that impact App Size" 
---

In the previous posts we talked about [Build Pipeline](http://localhost:1313/posts/devtools/optimizing-app-size/build-pipeline/), [Jargon](http://localhost:1313/posts/devtools/optimizing-app-size/jargon/), [Static Linker](http://localhost:1313/posts/devtools/optimizing-app-size/how-does-the-linker-help-reduce-app-size-part-1/) vs [Dynamic Linker](http://localhost:1313/posts/devtools/optimizing-app-size/how-does-the-linker-help-reduce-app-size-part-2/). In this post we'll benefit from the knowledge gained about the app wrapper's folder structure and the placement of all the different binaries (frameworks and main app's executable) to know _where_ to look for. New in this post is learning _how to use the `nm` command to inspect and count the number of symbols of_ each binary.

## Symbols

### What are symbols?
- A symbol is a name to refer to a fragment of code or data
- Fragments of code may reference other symbols (e.g., when a function calls another function)

For more see [WWDC 2018 - Behind the Scenes of the Xcode Build Process](https://www.wwdcnotes.com/notes/wwdc18/415/)


### Why do symbols exist? 

- Cross-binary communication
- Debugging information
    - Type, function, variable names
    - File name, file number
    - Mappings between symbols and their addresses

### When and where are symbols generated? 
Any type, variable, function you define turns into a symbol. 
They get generated within an [object file](https://mfaani.com/posts/devtools/optimizing-app-size/jargon/). A typical object file contains: 

- Compiled Code
- Symbol Table
    - Global symbols
    - Undefined symbols
    - Debug symbols
    - Swift symbols
- DWARF

### Can you explain the above symbols a bit more? 
- Think of global symbols as the public interface. For frameworks this should not get stripped. For apps you can strip them as long as you don't need the public interface for unit-testing. If you strip the global symbols for a framework then you're not exposing the binary's interface to others binaries that need to communicate with your binary. 
- Undefined symbols are symbols that don't exist within the binary. They're a dependency of the current binary. They should NOT be stripped. Otherwise the linker expects them to exist and things would fail. 
- Debug symbols are used to generate the dSYM. They must get stripped (only after you've extracted the dSYM). Otherwise it would cause bloat in your binary. Xcode should handle this correctly but often things get messed up. 
- Swift symbols are used to make things compile. Think of them more as scaffolding. Once the binary is compiled, they must get stripped. Otherwise it would cause bloat in your binary. Xcode should handle this correctly but often things get messed up. 

NOTE: The stripping of these symbols are governed by Xcode Build Settings. But it's beyond the scope of this article. The focus of this article is to examine a binary after it's been made ready for the App Store. 

### So some symbols need to get stripped? 
Yes. Some. Debug symbols should get stripped after you have the dSYM created. Swift symbols should get stripped after compilation as well. They're both redundant. 

Xcode strips Release builds for you. So you usually don't have to care unless things are misconfigured.

### So how can I see the symbols of a binary? 

That's where the `nm` command becomes useful. 

## `nm` command

### What does the nm command do?

`nm` is short for names. Names refers to the name/symbol of each address. 

Just pick any [binary](https://mfaani.com/posts/devtools/whats-the-difference-between-an-app-bundle-and-a-binary/) and just do:

```yaml
nm -a <your binary> # List all symbols in the binary
nm -g <your binary> # List all globals in a binary (anything that's made public)
nm -u <your binary> # List only undefined symbols in a binary. (any symbol which you import)
```


### What are some ways to get a more granular view into the symbols? 

Get count of **Debug** symbols:

```yaml
nm -a <your binary> | grep - | wc -l 
```

I'm grepping on `-` because debug symbols are annotated as such. See `man nm` in Terminal for more.

Get count of **Swift** symbols:

```yaml
nm -a <your binary> | grep '_$s' | wc -l # don't forget the single quotes
```

I'm grepping on  `'_$s'` (or often with `_$S`) because Swift symbols are annotated as such. See `man strip` in Terminal for more information.

### Does the `nm` command give you size? 

No. It just shows you the symbols. Doesn't give you any information about their length. Your focus should be more on the number of symbols.   
Typically Swift symbols are long and that causes size increases. 

**What you can do instead is that just compare the actual size of the binary for before and after stripping.**

## Stats & Tips

This post isn't to say what's expected to seen. More on how to inspect symbols. And have a way to validate the impact of changes you made. 

You might think well I have to go through the entire app archive process and that's a lengthy process. You might have to do that ultimately. But an alternate way is to just:

1. Compile a sample file with `swiftc` and pass different commands e.g. try passing `-g` to create the dSYM. 
2. Record the count of the Debug Symbols and Swift Symbols using `nm`. 
3. After recording, attempt to strip the binary. 
4. Then record the count of Debug Symbols and Swift Symbols again
5. Compare the counts with before stripping along with the binary sizes.  
6. Rationalize on how things work. Use the steps mentioned in the image from [Xcode Build Pipeline](https://mfaani.com/posts/devtools/optimizing-app-size/build-pipeline). Inspect your Build Settings for each individual target. 

For a small swift file I did the steps The results were as such: 


### Examples

#### File 1
```swift
import Foundation 
let arguments = CommandLine.arguments

if arguments.count != 3 {
    print("Error. Use: Printer firstName lastName")
    exit(1)
}

/// - Note: The value of arguments[0] is the name of the binary
let firstName = arguments[1]
let lastName = arguments[2]
print("Greetings \(firstName) \(lastName)")
```

#### Compiling and inspecting WITHOUT debug information

```yml

# Build
swiftc main.swift 

# Execute
./main Ethan Hawk # Greetings Ethan Hawk

# Inspect
nm -a main | wc -l # 43. Total number of symbols. 
nm -a main | grep '_$s' | wc -l # 23 Swift symbols. 
nm -a main | grep - | wc -l # 0 Debug symbols

# Strip
strip -ST main # S flag removes Swift symbols. T flag removes debug symbols. 
nm -a main | grep '_$s' | wc -l # 0. Successfully nuked all Swift symbols.
```

#### Compiling and inspecting WITH debug information

```yml
# Build
swiftc -g main.swift # Creates a binary named 'main' + main.dSYM

# Inspect
nm -a main | wc -l # 84. We got more than before (43) because we're creating some extra debug symbols.
nm -a main | grep '_$s' | wc -l # 31 Swift symbols. Which is slightly more than 23
nm -a main | grep - | wc -l # 41 debug symbols. This is substantially higher than 0.

# Strip

strip -ST main
nm -a main | grep - | wc -l # 1 Debug symbols.
nm -a main | grep '_$s' | wc -l # 0 Swift symbols. 
```

Apps in the app store are always compiled with dSYMs. And must therefore be stripped. 

## Actual Stats
**The lack of correct stripping could lead to a ginormous 30% increase for frameworks (and app) binaries as mentioned [here](https://github.com/CocoaPods/CocoaPods/issues/10277).**

For a sample framework when misconfigured:
- There were about 125000 debug symbols. After correct stripping it went down to 500.
- There were about 80000 Swift symbols. After correct stripping it went down to 7000 symbols.
- The framework went down from 35MB to 24MB after correct stripping.


## Summary
### When using Xcode 
- Find either the build folder or archive, then find the .app wrapper and within it inspect each binary. 
- If you create a dSYM then Xcode will add more symbols.
- For Release builds, if things are correctly configured then it will strip the extra symbols using `strip` command. Otherwise it won't strip and you'd have a lot of bloat. 
- You can inspect the symbols of each binary using `nm`. Use grep to filter for extraneous Debug or Swift symbols.
- If you have dynamic libraries, then you must inspect each binary individually. 

**NOTE:** The build settings (which affect debug symbols and other speed and size optimizations) are different between Release and Debug builds. As a result make sure you check the App Store build. Not anything else. 

### When using `swiftc` in command line
- When using `swiftc` you must pass the required options (`-g`) for `swiftc` to create the dSYM. 
- You also have to use `strip` along with appropriate options (`-ST`) to strip. 

Xcode does a lot of things for you without you realizing it. If you try things with `swiftc` then you realize more of the details yourself.



## Action Items
My post isn't meant to say you're doing things wrong nor that by following its steps you'll save 30% on App Size. Rather it's just a way to be able inspect things. Often Dependency tools such as Cocoapods, Carthage, make minor mistakes that are hard to find or even a small change in an Xcode update ([Emerge Tools - How Xcode 14 unintentionally increases app size](https://www.emergetools.com/blog/posts/how-xcode14-unintentionally-increases-app-size)) might tweak things that are incompatible with your current setup and create issues that are hard for you to figure out. The tips above just might help you inspect and narrow things down easier or help with becoming better at having an x-ray view of the build pipeline and the impact of certain settings. 

## Acknowledgements

Major shout out to [Mark Rowe](https://twitter.com/bdash) who answered a lot of my questions about `nm` so I can put together this post. His exceptional [RTFM](https://en.wikipedia.org/wiki/RTFM) abilities was helpful for figuring out how to make `nm` and `strip` commands work.
