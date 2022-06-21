---
title: "CurrentValueSubject Example"
date: 2022-04-14T15:20:02-04:00
categories: [Combine]
tags: ['swift', 'currentValueSubject', 'combine']
cover:
    image: "dispose-bag.jpg"
    alt: "dispose bag"
    relative: true
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

I googled a bit for "CurrentValueSubject Example". Surprisingly I wasn't able to find a simple answer. So I created a few examples:

## Basic
```swift
import UIKit
import Combine

class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()        
    }
    
    func setup() {
        let _ = name.sink { value in
            print(value) // Jason, Jason Bourne
        }
        name.send("Jason Bourne")
    }
}
```

Basically every time you call `send` on a publisher, the subscriber gets a callback. 

## Debugging

Notice the `print("debug - ")`. It just helps us see the steps. 

```swift
import UIKit
import Combine

class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()        
    }
    
    func setup() {
        let sub = name.print("debug - ").sink { value in
            print(value) // Jason, Jason Bourne
        }
        name.send("Jason Bourne")
    }
}
```

Output:
```
debug - : receive subscription: (CurrentValueSubject) # It has a subscription
debug - : request unlimited # The subscriber wants to know of every value the publisher emits. It doesn't want to stop after a certain number.
debug - : receive value: (Jason) # A value of 'Jason' was received' 
debug - : receive value: (Jason Bourne) # A value of 'Jason Bourne' was received' 
debug - : receive cancel # The subscription was terminated.
```

## Deinitialization gotcha - no retain

```swift
import UIKit
import Combine

class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()        
    }
    
    func setup() {
        let _ = name.sink { value in
            print(value) // Jason. WILL NOT necessarily be called for 'Jason Bourne'
        }
        name.send("Jason Bourne")
    }
}
```

Here the subscription is valid only until the end of the `setup` function. However when you use `_` you're telling the compiler that I care less and it may be deinitialized immediately. 

Generally the only way to guarantee your subscriptions stay alive is to reference them from a lexical scope that outlives the time you want to receive subscriptions.

The [docs](https://developer.apple.com/documentation/combine/publisher/sink(receivevalue:)) on `sink` also say: 

> This method creates the subscriber and immediately requests an unlimited number of values, prior to returning the subscriber. The return value should be held, otherwise the stream will be canceled.

If you try using the Debugging technique, you'll realize that the subscription is canceled before `name.send("Jason Bourne")` is reached. 

## Deinitialization gotcha - scope exit

```swift
import UIKit
import Combine

class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) { [weak self] in
            self?.name.send("Delayed name")
        }
    }
    
    func setup() {
        let sub = name.sink { value in
            print(value) // Jason, Jason Bourne. Will NOT be called for 'Delayed name' 
        }
        name.send("Jason Bourne")
    }
}
```

'Delayed name' will not get printed, because the subscription that happened after `setup()` is cancelled once `sub` goes out of scope.

## Solution

```swift
class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()
        DispatchQueue.main.asyncAfter(deadline: .now() + 2) { [weak self] in
            self?.name.send("Delayed name")
        }
    }
    
    var sub: AnyCancellable? 
    
    func setup() {        
        sub = name.sink { value in
            print(value) // Jason, Jason Bourne, Delayed name
        }
        name.send("Jason Bourne")
    }
}
```

`sub = name.sink {...}` is a subscription. It really need to return a type. It just happens to do that so:
- You get a reference to it. Therefore have some level of control over how/when you can cancel the subscription.
- Because you gave it a name. i.e. you didn't just do `name.sink {...}` or `_ = name.sink{...}` then the subscription will remain seeking events until the subscription goes out of scope. For the above example that's when `sub` property goes out of scope i.e. when the viewController deinitilizes. 

### But doesn't the subscription remain after the object is deallocated? 
Good question. Docs on `AnyCancellable` say: 

> An `AnyCancellable` instance automatically calls `Cancellable/cancel()` when deinitialized.
tldr within its `deinit` it will call `cancel`.

## Accessing value & Updating value

```swift

var name = CurrentValueSubject<String, Never>("Jason")
print(name) // 😐 Combine.CurrentValueSubject<Swift.String, Swift.Never>
print(name.value) // Jason

name = "David" // ❌ ERROR: Cannot assign value of type 'String' to type 'CurrentValueSubject<String, Never>'
name.send("David") ✅
```

It's also named as _current_ valuesubject because:
- As demonstrated above, you can always access its **current** value using `.value`. 
- Upon subscription, the subscriber will get a callback for the _current_ value of the publisher. 

You can think of `CurrentSubjectValue` as `APublisherThatItCurrentValueIsAccessibleAndWillEmitItsValue`. For more on that see [here](https://stackoverflow.com/a/60563199/5175709)


## Alternate solution

```swift
import UIKit
import Combine

class ViewController: UIViewController {
   
    var name = CurrentValueSubject<String, Never>("Jason")
    var subscriptions: Set<AnyCancellable> = []
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setup()
    }
    
    func setup() {
        name.sink { value in
            print(value) // Jason, Jason Bourne
        }.store(in: &subscriptions)
        
        name.send("Jason Bourne")
    }
}
```

Remember we said that we should [retain the return value of a subscription](https://mfaani.com/posts/currentvaluesubject-example/#deinitialization-gotcha---no-retain)? 

There's a cleaner way other than doing `let sub = name.sink {...}`

You could just store the subscription using [`store(in:)`](https://developer.apple.com/documentation/combine/anycancellable/store(in:)-3hyxs)

This way you don't create multiple (not really wanted) objects just so you could retain the subscription. You just store one variable and then dump all the subscriptions into it. 

### Can you explain what `var subscriptions: Set<AnyCancellable> = []` is again?

It might helpful if we named the variable differently. Alternate names are: 

```
// just to retain all our subscriptions
var subscriptionRetainer: Set<AnyCancellable> = [] 

// a group of cancelable items. Remember every subscription is cancellable. That's why often the names are used interchangeably 
var cancellables: Set<AnyCancellable> = []  

// a place to just dump my subscriptions and not care too much.
var subscriptionDumpster: Set<AnyCancellable> = [] 

// a bag (of subscriptions) that is soon to be disposed. This is how RxSwift used to name things. 
var disposableBag: Set<AnyCancellable> = [] 

// self-explanatory
var storageForAllOurSubscriptionsSoWeDon'tCreatVariablesForThem: Set<AnyCancellable> = [] 
```

All the names above are for the same purpose. It's just that people name it differently. 

Beyond that, you could use it for grouping subscriptions. Example: 

```swift
var primaryUserSubscriptions: Set<AnyCancellable> = [] 
var secondaryUserSubscriptions:  Set<AnyCancellable> = [] 
```

### What's with the cover image of the post? 
It's an image of disposable bags. Just like how some name the set of their subscriptions. 😀

### OK. I get what `Set<AnyCancellable>` does. But why not just use the normal subscription I had before?
It's just a convenience. That is:
- All subscriptions get to live as long as the set is living.
- You don’t have to create variables just so you can increase the life-cycle of the subscription.

All that said, often a single value can still be useful if you need to shorten the lifetime. Example if you want to only subscribe after `viewWillAppear` and cancel after `viewWillDisAppear`

### Does `Set<AnyCancellable>` help avoid memory leaking? 

The concept of disposeBag / having a Set<AnyCancellable> variable, is not about leaking memory i.e. it’s not about ending subscriptions when an object goes out of memory. That will happen automatically. 

The main purpose is to retain the subscription until the object is in memory (or until you call `cancel` yourself)
Because if you don’t use store your subscriptions (use dispose bag), your subscriptions will go out of memory right away (hence no leaking) as shown earlier.

### Anything else about `Set<AnyCancellable>`? 

You almost always want it to be a `private` variable. It really has no purpose outside its current class. 

## Summary
- Use `send` to update values. 
- Use `print()` like `name.print("a prefix").sink{...}` to see what's happening under the hood.
- Your subscription needs to be retained, otherwise your subscriptions would get canceled either immediately or upon exiting the current scope. 
- A subscription returns an `AnyCancelleable`. The reason for that is to give you control over the scope/duration of the subscription. It's not about leaking memory.
- `AnyCancellable` automatically calls `cancel` when deinitialized.
- `Set<AnyCancellable>` offers a nicer API that helps reduce clutter in your code. 
- `Set<AnyCancellable>` is not the solution for every kind of subscription you have. 
- Engineers name their `Set<AnyCancellable>` different things. Yet the purpose of it identical across all engineers. 
- Almost all the time you want your `Set<AnyCancellable>` to be a `private` variable.