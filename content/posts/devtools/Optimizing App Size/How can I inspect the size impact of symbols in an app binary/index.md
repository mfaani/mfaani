---
title: "How Can I Inspect the Size Impact of Symbols in an App Binary: A Practical Guide for Apple Developers"
date: 2023-08-11T16:21:06-04:00
category: "Devtools"
tags: [nm, symbols, App Size, iOS, macOS, swiftc, dSYM, strip]
ShowToc: true
description: "Learn how to use nm and more importantly count symbols" 
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

## `nm` command

### What does the nm command do?

`nm` is short for names. Names refers to the name/symbol of each address. 

Just pick any [binary](https://mfaani.com/posts/devtools/whats-the-difference-between-an-app-bundle-and-a-binary/) and just do 

```yaml
nm -a <your binary> # List all symbols in the binary
nm -g <your binary> # List all globals in a binary (anything that's made public)
nm -u <your binary> # List only undefined symbols in a binary
```

### What are some ways to get a more granular view into the symbols? 

Get count of **Debug** symbols:

```yaml
nm -a <your binary> | grep - | wc -l 
```

I'm grepping on `-` is because debug symbols are annotated as such. See `man nm` in Terminal for more.

Get count of **Swift** symbols:

```yaml
nm -a <your binary> | grep '_$s' | wc -l # don't forget the single quotes
```

I'm grepping on  `_$s` (or often with `_$S`) because Swift symbols are annotated as such. See `man strip` in Terminal for more information.

## Stats & Tips

This post isn't to say what's expected to seen. More on how to inspect symbols. And have a way to validate the impact of changes you made. 

You might think well I have to go through the entire app archive process and that's a lengthy process. You might have to do that ultimately. But an alternate way is to just compile things with `swiftc` and pass different commands e.g. try passing `-g` to create the dSYM. Then record the count of the Debug Symbols and Swift Symbols. After recording, attempt to strip the binary. Then record the count of Debug Symbols and Swift Symbols again and compare it with before stripping. For a super small swift file I did this. The results were as such: 

### Examples

#### File 1
```swift
import Foundation 
let arguments = CommandLine.arguments

if arguments.count != 3 {
    print("Error. Use: Printer firstName lastName")
    exit(1)
}

/// -Note: The value of arguments[0] is the name of the binary
let firstName = arguments[1]
let lastName = arguments[2]
print("Greetings \(firstName) \(lastName)")
```

#### Compiling and inspecting WITHOUT debug information
```yaml
# Build
swiftc main.swift # creates a binary named 'main'

# Execute
./main Ethan Hawk # Greetings Ethan Hawk

# Inspect
nm -a main | wc -l # 43. Total number of symbols. 
nm -a main | grep '_$s' | wc -l # 23 Swift symbols. 
nm -a main | grep - | wc -l # 0 Debug symbols

# Strip
strip -ST main # with extra flags. S flag removes Swift symbols. T flag removes debug symbols. 
nm -a main | grep '_$s' | wc -l # 0. Successfully nuked all Swift symbols.
```

#### Compiling and inspecting WITH debug information

```yml
# Build
swiftc -g main.swift # Creates a binary named 'main' + main.dSYM

# Inspect
nm -a main | wc -l # 84. We got more than 43 because we're creating symbols, specifically debug symbols
nm -a main | grep '_$s' | wc -l # 31 Swift symbols. Which is slightly more than 23
nm -a main | grep - | wc -l # 41 debug symbols. This is substantially higher than 0.

# Strip

strip -ST main
nm -a main | grep - | wc -l # 1 Debug symbols. Not sure why there's a leftover. But since it's just 1, I suppose it's fine. 
nm -a main | grep '_$s' | wc -l # 0 Swift symbols. 
```

This could lead to a ginormous 30% size saving as mentioned [here](https://github.com/CocoaPods/CocoaPods/issues/10277).

#### File 2

```
public struct Home {
    var city: String
    public var streetAddress: String

    static let planet = "Earth"

    func turnOn() {}
}
```

Using `nm` slightly different: 

```
swiftc Home.swift -o Home
nm -am Home | xcrun swift-demangle
```

- The `xcrun` helps the OS figure out which Xcode version is required to be used. 
- `swift-demangle` helps with demangling mangled symbols. 

Typically the structure of an `nm` result would be like: 


- Symbol value.
- Symbol type.
- Symbol name.

If the `nm`` command was to add column names for its output, I think the following column names would be appropriate:

**Address:** The symbol value in hexadecimal.
**Type:** The symbol type.
**Name:** The symbol name.
The Address column would be useful for identifying the location of the symbol in the object file. The Type column would be useful for understanding the nature of the symbol, such as whether it is a global or local symbol, or whether it is initialized or uninitialized data, external vs non-external. The Name column would be useful for identifying the symbol and its purpose.

I've selected a few of the lines from `nm - am <binary> | xcrun swift-demangle`

```
Address                       Type                    Name
0000000100003868      (__TEXT,__text) external      Home.Home.streetAddress.setter : Swift.String

00000001000037ac      (__TEXT,__text) non-external (was a private external) Home.Home.city.getter : Swift.String

0000000100008008 (__DATA,__common) non-external (was a private external) static Home.Home.planet : Swift.String
```


Export to Sheets
The Address column would show that the symbol is located at address 0x00000001000038b4. The Type column would show that the symbol is a non-external symbol, which means that it is not accessible to other objects. The Name column would show that the symbol is named _$s4HomeAAV13streetAddressSSvM.







## Summary 
This doesn't mean you're currently doing things wrong and will save 30% now. Rather it's just a way to be able inspect things. Often Dependency tools such as Cocoapods, Carthage, make minor mistakes that are hard to find or even a small change in an Xcode update ([Emerge Tools - How Xcode 14 unintentionally increases app size](https://www.emergetools.com/blog/posts/how-xcode14-unintentionally-increases-app-size)) might tweak things that are incompatible with your current setup and create issues that are hard for you to figure out. The tips above might help you narrow it down easier. 

## Acknowledgements

Major shout out to [Mark Rowe](https://twitter.com/bdash) who answered a lot of my questions about `nm` so I can put together this post. His exceptional [RTFM](https://en.wikipedia.org/wiki/RTFM) abilities was helpful for figuring out how to make `nm` and `strip` commands work.
