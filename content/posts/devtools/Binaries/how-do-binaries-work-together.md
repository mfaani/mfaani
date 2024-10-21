---
title: "How Do Binaries work together? What breaks ABI?"
date: 2024-10-20T15:22:35-04:00
description: "Ever wondered what how two binaries interface with one another? Which changes impact ABI and which don't?"
category: "Devtools"
tags: ["compiler", "ABI", "API", "Application Programming Interface", "Application Binary Interface", "dyld", "Breaking Change"]
ShowToc: true
---

For two (dynamic) libraries to work together they need: 
- API compatibility
- ABI compatibility

If you have correct function, parameter names, and are able to access it (it's not `private`) then you're good. The **compiler** validates if classA is using the correct Programming Interface from classB 👈 API (Application Programming Interface)

## Example of API usage

```swift
struct House {
    var address = Address(streetAddress: "1100 Happy St.")
}

struct Person {
    var address = Address("1100 Sad St.") // ‼️ Missing argument label 'streetAddress:' in call
}

struct Address {
    let streetAddress: String
}
```

ABI compatibility is similar to API, but just at the *binary* level. The compiler is no longer involved. What's involved is the dynamic linker `dyld`. 

It checks to see if the [undefined symbols - symbols that are to be provided by another binary](https://mfaani.com/posts/devtools/optimizing-app-size/how-can-i-inspect-the-size-impact-of-symbols-in-an-app-binary/#can-you-explain-the-above-symbols-a-bit-more) can be found in another dynamically linked library. `dyld` checks if binaryA is using the correct Binary Interface from binaryB 👈 ABI (Application Binary Interface)


### What is the purpose of ABI Stability?

Dynamic linking is our goal, and ABI stability is just a means to that end - [How Swift Achieved Dynamic Linking Where Rust Couldn't - Aria Desire](https://faultlore.com/blah/swift-abi/)

## Example of ABI usage

Library A

```swift
import LibB
struct Car {
    let map = Map()
    map.start(accuracy: .high)
}

```

Library B

```swift
public struct Map {
    public func start(accuracy: Accuracy)
}

public enum Accuracy {...} 
```

Once the two libraries are compiled, then inspecting `libA`'s symbols using `nm` (see [my other post](https://mfaani.com/posts/devtools/optimizing-app-size/how-can-i-inspect-the-size-impact-of-symbols-in-an-app-binary/#nm-command) for more), we'd see undefined symbols. Among the list of outputs we'd see something similar to this: 

```
U _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq 
U _$s4LibB8AccuracyO4highyA2CmF

```

> `U _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq`: Is the mangled name for the `Map.start(accuracy:)` method in Swift.  
> `U _$s4LibB8AccuracyO4highyA2CmF`: Is the mangled name for the Accuracy enum in Swift, also undefined in libA and defined in libB.  
> The `U` indicates that it is _undefined_ in libA and must be provided by libB and that things will be resolved by dynamic linking.  

Similarly, if we inspected libB, then we'd the same symbols, but as such: 

```
T _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq 
T _$s4LibB8AccuracyO4highyA2CmF
```

`T` indicates that it is a defined function or method. For more on that see [here](https://github.com/aidansteele/osx-abi-macho-file-format-reference?tab=readme-ov-file#table-1-the-sections-of-a__textsegment) and [here](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/CodeFootprint/Articles/MachOOverview.html)

## What breaks ABI?
- Removal of existing API: Removing a public function, type or variable.
- Changes of existing API: Renaming a public function, type or variable OR adding an additional parameter to a function. 
- Reduction of access control: Making a `public` function, type of variable `internal` or `private`
- Adding to protocol requirements: Adding a new function to the protocol.
- Using a much newer or older Swift compiler which causes the symbols between the binaries to mismatch. See [here](https://stackoverflow.com/questions/58654714/module-compiled-with-swift-5-1-cannot-be-imported-by-the-swift-5-1-2-compiler)
- etc. 

## Would it still be a breaking change if you add a new parameter but gave it a default value? 

Changing `func start(accuracy: Accuracy)` to `LibB.Map.start(accuracy: Accuracy, distance: Int = 10)`  breaks the ABI. It doesn't break API though. Let's explain that:

### LibA precompiled: 
Assume LibA is pre-compiled. It was only aware of `LibB.Map.start(accuracy: Accuracy)`'s symbol at the time of its complication, even if you give the function a default value, because the symbols are different, LibA and LibB are incompatible. 

> LibA needs to get **recompiled** so its binary is aware of the new symbol. Code change is not needed though. Upon recompilation, LibA **learns of the updated symbol**. Without recompilation, LibA would be still looking for the *old* symbol which doesn't have the `distance` parameter. The table below shows the *lack* of impact of default values on symbol table.

|   | LibB v1 | v2.a LibB with new param | v2.b LibB with new param and default value |
| - | ---- | ------------------- | ------------------------------------- |
| Syntax | `LibB.Map.start(accuracy: Accuracy)` | `LibB.Map.start(accuracy: Accuracy, distance: Int)`| `LibB.Map.start(accuracy: Accuracy, distance: Int = 10)` |
| Symbol | `T _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq` | `T _$s4LibB3MapVAA8AccuracyO8distanceSi4startyyF` | `T _$s4LibB3MapVAA8AccuracyO8distanceSi4startyyF` |

💡 The symbol of the 2nd and 3rd column are the same.  
💡 Information about the default value doesn't get carried within the symbol tables. 

### LibA - source code: 
Assume LibA is not _pre-compiled_. You have access to its _source code_ and are always compiling it when you run/build/archive the app. After you pull in the latest version of LibB, you re-compile (as you always do for every run) LibA with `LibB.Map.start(accuracy: Accuracy, distance: Int)`. Although _technically_ a breaking change, since code change isn't necessary and you're always re-compiling then it _appears_ so that it's not a breaking change. 

#### Wait what? So it's a breaking change only if libA is precompiled? 

It's **always** a breaking change when the change adds a new parameter to an existing function. Once this happens the next version change should be a major version bump. Because your dependent libraries only know how to handle the _old_ symbol. Yet the problem manifests itself only when your dependent library is **pre-compiled**. In the Apple world, pre-compiled binaries come in the form of xcframeworks. 

Every tag/commit on you library can be either delivered as source-code or precompiled. As a result you must version your code with the assumption that it could be either. Even if you shipped pre-compiled, there's nothing holding back _another_ developer giving access to source code for a specific tag.

That being said, I've added new parameters to functions with default values without doing major version bumps. Although this was a mistake, everything worked out because the dependent library/app was using the library's _source code_ directly.

### So adding a default value doesn't do anything?
Default values help with API. Not with ABI. As illustrated the above table, default values have no impact on ABI. This is because in most languages, a function is uniquely identified by its name, and its parameters. Both the argument labels, and the types. For more on that see this [WWDC Session - Binary Frameworks in Swift](https://developer.apple.com/videos/play/wwdc2019/416/?time=1339)

## How can I not make a breaking change when adding new parameters to my functions?

Assume you had the following

```swift
public struct Map {
    public func start(accuracy: Accuracy)
}
```

Then go to: 

```swift
public struct Map {
    public func start(accuracy: Accuracy) {
        start(accuracy: Accuracy, distance: 10)
    }

    public func start(accuracy: Accuracy, distance: Int)  { ...}
}
```

By doing 👆 you haven't changed your ABI, rather you've just made an *addition*.


## Are there any breaking changes that are't originated from a change in the API/ABI?
Yes. If you change the *behavior* of something. Examples:
  - **Performance Change:** A function used to take 0.3 seconds but now takes 6 seconds
  - **Semantic Change:** A function that takes `a`, `b` as inputs and returns `c`, but after a change returns `d`. This is a breaking change. 
  - **Behavioral Change:** A function that used to fire notifications and now it doesn't. Or previously it didn't fire any notifications to a known channel but now it does.
  - **Thread Safety:** Introducing or removing thread safety in a function can also be a breaking change. For example, making a function that was previously thread-safe no longer thread-safe, or vice versa.
  - **Resource Management:** Changing how resources are managed or cleaned up within a function. For example, if a function that previously did not close file handles now closes them, it can impact the overall resource management in an application. Or if a function previously used 1% battery, but now uses 5%.
  - Not a breaking change, but worth mentioning that you can't compile a binary for the macOS *platform* but then expect it to work for the Linux platform. The binary has to be for the appropriate platform.

It's better to mark all the above changes as a major version change along with proper release notes. 

## How does a binary written in `Swift` work with a binary written in `Rust`?

Not every two binaries can work together. The ABI between the two need to understand one another. This is assisted by a [ffi](https://en.wikipedia.org/wiki/Foreign_function_interface). FFIs assist with exposing the interface of one library to the other. Usually the interface is on top of C, since C is a widely used low-level language. 

> A foreign function interface (FFI) is a mechanism by which a program written in one programming language can call routines or make use of services written or compiled in another one.  
> It mates the semantics and calling conventions of one programming language (the host language, or the language which defines the FFI), with the semantics and conventions of another (the guest language).

You basically have to spend extra effort and use the ffi to create the interface. This is something that you don't have to do for two Swift binaries, because it's all automatic. 
## Summary

API is about correct mapping of Programming Interface. ABI is about correct mapping of symbols. Symbols are based off of function name, parameter names and parameter types. Default values don't show up in symbols. 

Adding a new parameter with a default value is still a breaking change. There were lots of other ways to break binary compatibility. It's important to be able to identify these and do major version bumps when needed.  

Last but not least, often you've made a breaking change but your [build process masks it and helps you recover from it](http://localhost:1313/posts/devtools/binaries/how-do-binaries-work-together/#liba---source-code). Be sure to still do a major bump.