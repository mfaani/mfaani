---
title: "Why can't Xcode show the caller?"
date: 2022-01-04T22:26:19-05:00
slug: "why-cant-xcode-show-caller"
category: ['Swift','Xcode']
tags: ['swift', 'swift-protocols', 'compile-time-check', 'Xcode']
# need to upload the project without any git stuff...
---

Download the [sample project](https://github.com/prohoney/xcode-cant-show-caller-sample) if you want. You don't have to though.
The project doesn't even need to be ran. It's just provided for context. 

I'm going to discuss two gotchas I hade with Xcode. For quite some time I thought a fix was coming, then I realized this is because I didn't fully understand the difference between an interface (or protocol) with a concrete type. 

### Find Call Hierarchy not working:

Have you ever right clicked on a function -> Find -> Find Call Hierarchy, but then wondered why Xcode doesn't show you where the function is getting called from? 

![Find Call Hierarchy](images/find-call-hierarchy-confusion.png "Xcode - Find Call Hierarchy doesn't show callers")

The reason that such a complication exists is Swift Protocols. The compiler is very precise. 

- When you try to look up 'Find Call Hierarchy' on a **concrete type**, Xcode tries to look things up by finding a call to that very implementation. 
- When you try to look up 'Find Call Hierarchy' on a **protocol type**, Xcode tries to look things up by finding a call to all conforming implementations. 


```swift
class Logger: LoggerProtocol {
    func performLog() {
        print("Production Code")
    }
}
```

Trying to find all callers of `performLog` from above results in Xcode looking up _references_ for `Logger.performLog`. Usually there aren't much callers that directly hit a concrete implemenation of a function. Xcode found zero callers!

![Jump to Definition](images/find-call-hierarchy-on-class.png "Xcode - Find call hierarchy on a class/concrete type, doesn't find callers")

While if you try finding the caller by write clicking the function signature under the protocol then things are different: 

```swift
protocol LoggerProtocol {
    func performLog()
}
```

It's because Xcode will look up _references_ for `LoggerProtocol.performLog` (vs. `Logger.performLog`).

Usually there are a lot of conforming types that implement a protocol function.  In this sample project that I wrote, Xcode only found one caller:

![Jump to Definition](images/find-call-hierarchy-on-protocol.png "Xcode - Find call hierarchy on a protocol type,can find callers")

### Jump to Definition not working:

Similarly, have you ever right clicked on a function call -> Jump to Definition -> Then get prompted with too many definitions?

![Jump to Definition](images/jump-to-definition-confusion.png "Xcode - Jump to Definition hell!")

When it comes to protocols, the compiler just can't tell _which_ adaptation of a protocol gets used e.g. upon tapping 'Jump to Definition' on `performLog` in the snippet below, Xcode will show all conformances (production adoption, test-code adoption, another production adoption, etc.) to that _protocol_ requirement.

```swift
struct ViewModel {
    var logger: LoggerProtocol
    
    func executeLog() {
        logger.performLog()
    }
}
```

Xcode leaves it up to you to decide _which_ class definition/implementation is the one you're looking for. 

![jump-to-defintion](images/jump-to-definition.png "Xcode - leaves it up to the developer to decide which implementation they want")

Usually you don't want the protocol definition nor want the mock implementation. Rather you just want one of the concrete implementations that is for production code.

### Runtime advantages

During runtime Xcode can actually identify the implementation is uses. Obviously if that wasn't the case then Xcode won't know which implementation to execute. The advantage of this is that if you use Xcode's Step Into ({{< rawhtml >}}
<img src="images/IB_Debug_StepInto_2x.png" alt="Step into" width="21" height="21" style="display:inline; vertical-align:bottom">{{< /rawhtml >}} ) on a function then it will take you to the right implementation.

![Step into on breakpoint](images/step-into-on-breakpoint.png "Xcode - Step into on `perfromLog` takes you to correct implementation")

Will take you to: 

![Xcode Finds correct code](images/finds-right-code.png "Xcode - found implementation")

## Conclusion: 

Xcode can't automagically identify which implementation of a protocol is used. Additionally when a _concrete_ type is never made part of the contract/API/signature, then it won't be used in Xcode's lookups that happen against the _protocol_ type. Using this insight we can better understand the behavior of Xcode and use its tools in the right way. 