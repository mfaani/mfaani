---
title: "How Do Binaries work together?"
date: 2024-06-22T15:22:35-04:00
description: "Ever wondered what how two binaries interface with one another? What impacts ABI and what does not? Also explain how it's different from API"
category: "Devtools"
tags: ["compiler", "ABI", "API", "Application Programming Interface", "Application Binary Interface", "dyld", "Breaking Change"]
ShowToc: true
draft: true
---

Let's start with how two (dynamic) libraries work together. 

It's similar to how two Swift classes work with each other. If you have correct function, parameter names, and are able to access it (it's not `private`) then you're good. The **compiler** validates if the correct contract is being used. It validates if classA is using the correct Programming Interface from classB <-- API (Application Programming Interface)

When you're at the binary level, then the compiler is no longer invovled. What's involved is the dynamic linker `dyld`. 

It checks to see if the [undefined symbols - symbols that are to be provided by another binary](https://mfaani.com/posts/devtools/optimizing-app-size/how-can-i-inspect-the-size-impact-of-symbols-in-an-app-binary/#can-you-explain-the-above-symbols-a-bit-more) can be found in another dynamically linked library. `dyld` checks if binaryA is using the correct Binary Interface from binaryB  <-- ABI (Application Binary Interface)


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
```e

Once the two libraries are compiled, then inspecting `libA`'s symbols using `nm` (see [my other post](https://mfaani.com/posts/devtools/optimizing-app-size/how-can-i-inspect-the-size-impact-of-symbols-in-an-app-binary/#nm-command) for more.), we'd see undefined symbols. Among the list of outputs we'd see something similar to this: 

```
U _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq 
U _$s4LibB8AccuracyO4highyA2CmF

```

> U _$s4**LibB**3**Map**VAA0C0VyAA8**Accuracy**O4**start**yAF_tFTq: This is the mangled name for the `Map.start(accuracy:)` method in Swift. The `U` indicates that it is _undefined_ in libA and must be provided by libB and that things will be resolved by dynamic linking
> 
> `U _$s4LibB8AccuracyO4highyA2CmF`: This is the mangled name for the Accuracy enum in Swift, also undefined in libA and defined in libB.


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
- etc. 

## Would it still be a breaking change if you add a new parameter but gave it a default value? 

Changing `func start(accuracy: Accuracy)` to `LibB.Map.start(accuracy: Accuracy, distance: Int = 10)`  breaks the ABI. It doesn't break API though. Let's explain that:

### LibA precompiled: 
Assume LibA is pre-compiled. It was only aware of `LibB.Map.start(accuracy: Accuracy)`'s symbol at the time of its complication, even if you give the function a default value, because the symbols are different, LibA and LibB are incompatible. 

LibA needs to get **recompiled** so its binary is aware of the new symbol. Code change is not needed though. Upon recompilation, LibA **learns of the updated symbol**. Without that things LibA is still looking for the old symbol which doesn't have the `distance` parameter.

|   | LibB v1 | v1.1.a LibB with new param | v1.1.b LibB with new param and default value |
| - | ---- | ------------------- | ------------------------------------- |
| Swift Syntax | `LibB.Map.start(accuracy: Accuracy)` | `LibB.Map.start(accuracy: Accuracy, distance: Int)`| `LibB.Map.start(accuracy: Accuracy, distance: Int = 10)` |
| Symbol | `T _$s4LibB3MapVAA0C0VyAA8AccuracyO4startyAF_tFTq` | `T _$s4LibB3MapVAA8AccuracyO8distanceSi4startyyF` | `T _$s4LibB3MapVAA8AccuracyO8distanceSi4startyyF` |

{{< rawhtml >}}<sub>Table 1 - API to ABI conversion </sub>  {{< /rawhtml >}}  

### LibA - source code: 
Assume LibA is not _pre-compiled_. You have access to its _source code_ and are always compiling it when you run/build/archive the app. After you pull in the latest version of LibB, you re-compile (as you always do for every run) LibA with `LibB.Map.start(accuracy: Accuracy, distance: Int)`. Although _technically_ a breaking change, since code change isn't necessary and you're always re-compiling then it _appears_ so that it's not a breaking change. 

#### Wait what? So it's a breaking change only if libA is precompiled? 

It's **always** a breaking change when the change adds a new parameter to an existing function. Once this happens the next version change should be a major version bump. Because your dependent libraries only know how to handle the _old_ symbol. Yet the problem manifests itself only when your dependent library is **pre-compiled**. In the Apple world, pre-compiled binaries come in the form of xcframeworks. 

Every tag/commit on you library can be either delivered as source-code or precompiled. As a result you must version your code with the assumption that it could be either. Even if you shipped pre-compiled, there's nothing holding back _another_ developer giving access to source code for a specific tag.


Having that said, I've added new parameters to functions with default values — without doing major bumps. This is a mistake, yet things compile fine because the dependent library/app was using the the source code of the library. 

### So adding a default value doesn't do anything?
Default values help with API. Not with ABI. As illustrated above in Table - 1, default values have no impact on ABI. This is because in most languages, a function is uniquely identified by its name, and its parameters. Both the argument labels, and the types. For more on that see this [WWDC Session - Binary Frameworks in Swift](https://developer.apple.com/videos/play/wwdc2019/416/?time=1339)


### Are there any breaking changes that don't break the API/ABI? 
- If you change the behavior of something. Examples:
  - **Performance Change:** A function used to take 0.3 seconds but now takes 6 seconds
  - **Semantic Change:** A function that takes `a`, `b` as inputs and returns `c`, but after a change returns `d`. This is a breaking change. 
  - **Behavioral Change:** A function that used to fire notifications and now it doesn't. Or previously it didn't fire any notifications to a known channel but now it does.
  - **Thread Safety:** Introducing or removing thread safety in a function can also be a breaking change. For example, making a function that was previously thread-safe no longer thread-safe, or vice versa.
  - **Resource Management:** Changing how resources are managed or cleaned up within a function. For example, if a function that previously did not close file handles now closes them, it can impact the overall resource management in an application. Or if a function previously used 1% battery, but now uses 5%.

All the above changes should be marked with a major version change.

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

By doing 👆 you haven't changed your ABI, rather you've just made additions.

## Summary

API is about correct mapping of Programing Interface. ABI is about correct mapping of symbols. Symbols are based off of function name, parameter names and parameter types. Default values don't show up in symbols. 

As a result adding a new parameter with a default value is still a breaking change. There were lots of other ways to break binary compatibility. It's important to be able to identify these and do major version bumps when needed. 