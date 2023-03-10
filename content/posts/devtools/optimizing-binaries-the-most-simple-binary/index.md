---
title: "Optimizing Binaries - The Most simple binary"
date: 2023-01-07T15:02:27-05:00
draft: true
---
# Series
In these series I will try to break down the build process. With the hope that you can understand the different steps of the build process and be able to answer to some of the following questions
- What impacts App Size? 
- What's the difference between Compilation and Linking? 
- What's a pre-compiled library? 
- How does an app include a library? 
- Do we create the Binary in the Pod's project itself? Or we create it during the client app's build process? 
- What's an archive? What's a .app file? What's a binary? 
- What resources does Apple provide for this? 
- How are the symbols baked into the app? How do we strip them? How do we symbolicate crash logs?
- How to inspect a binary?

In this first post, let's just try generating a binary/executable from a single swift file. 

# Example 
In the most simplest form, in order to create a binary you have to do: 

## Create a Swift file

For the sake of invoking the binary from command line, the file will take some arguments:

```swift
// Printer.swift
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
## Compile the file

Use [`swiftc`](https://stackoverflow.com/questions/57777091/whats-the-difference-between-swift-and-swiftc) to compile the file

```bash
swiftc printer.swift -o printer
```

## Invoke/run the binary
```
/.printer iOS Dev 
# Greetings iOS Dev
/.printer iOS Dev Xcode
# Error. Use: Printer firstName lastName
```

# How is a real app created? How is it different from what we just did above?

An app is built in similar ways. The differences for an _app_ are:
- There are hundreds of files from your app code.
    - Not every file is needed. So you may end up doing a selective loading and choosing only needed files and symbols.
- Usage of dependencies. 
    - A dependency can be either pre-compiled or available to you as source code. If source code, then you have to compile before your main app code compilation.
- More tools and file formats are used. Instead of building an app made of just swift files, you end up building an app from: 
    - Files
        - Swift files, images, objective-c code (`.m` files)
        - Other pre-compiled binaries    
        - `.a` for static library
        - `.dylib` for dynamic library
        - `.framework` for frameworks
    - Tools
        - Linker. Linker is the ultimate tool that combines several object files and libraries, resolves references, and produces an output file†.


†: Output File such as: 
- Binary
- dylib
- bundle
    - `.app` (Safari, Xcode, Chrome, Outlook). On your mac, you can find them within `/Applications`
    - `.framework` (Message.framework, WebKit.framework, System.framework, CoreImage.framework, Kernel.framework, etc.). On your mac you can find them within `/System/Library/Frameworks`

At the heart of everything is the linker. It's paramount to understand how it works. `ld` is the tool that gets used to link files together. You can also do `man ld` to learn more.

### If you run `swiftc` in verbose mode:
If you actually invoked `swiftc` with `-v` you'll see that it ends up calling `ld` to link all the object files together. 

The general idea is that if you have `main.swift`, `foo.swift`, `bar.swift`, `unused.swift` then the pipeline will be something like this: 

### Compilation Step
`main.swift` (depends on foo.swift) -> `main.o`
`foo.swift`  (depends on bar.swift) -> `foo.o`
`bar.swift`  (depends on CoolModule) -> `bar.o`
`qux.swift` -> `qux.o`

All files get compiled. To compile a file, all other files in the module will get parsed — if needed. 

### Linking Step 

The linker will then link all the necessary `.o` files. Meaning it starts with `main.o`, realizes it needs `foo.o`, then realizes it needs `bar.o`. 

Linker also sees that `bar.o` has an `import CoolModule`, so `CoolModule` also get linked too. That's it. Nothing links to `qux.swift` so it's object file won't get included in the final product. 

At the end the linker, links `main.o`, `foo.o`, `bar.o`, `CoolModule.a` all together and creates a binary. 

## How libraries are passed on to the linker?
It follows a similar paradigm of [Search Path](https://beautifulracket.com/setting-the-mac-os-path.html) (the `$PATH` env variable) in macOS. 

In the context of compiling code: 

A search path is a 'list of directories' that the compiler uses to search for header files, libraries, and other resources needed during the compilation process.

You can pass a search directory to `ld` using the `-L` flag along with a path

> `-L searchdir`
>
>`--library-path=searchdir`
>
>
> Add path searchdir to the list of paths that `ld` will search for archive libraries and `ld` control scripts. You may use this option any number of times. The directories are searched in the order in which they are specified on the command line. Directories specified on the command line are searched before the default directories. All `-L` options apply to all `-l` options, regardless of the order in which the options appear. `-L` options do not affect how ld searches for a linker script unless `-T` option is specified.

This is why in Xcode you often search path errors. 

# Summary
- Learned how to compile a single file using `swiftc`. For a deeper dive on that see this amazing post: [The Swift compiler for beginners
](https://theswiftdev.com/the-swift-compiler-for-beginners/)
- To build your app, your app doesn't just compile. It compiles and links. 
- Compiling goes from a .swift file to a .o file. See same link as above.
- Linker goes from a number of `.o` files and other file types to an executable. 
- Linker uses the same concept of search path as your mac does. See `man ld` for more.
For more on all the linker stuff see [BigNerdRanch - Manual Swift: Understanding the Swift/Objective-C Build Pipeline](https://bignerdranch.com/blog/manual-swift-understanding-the-swift-objective-c-build-pipeline/)


