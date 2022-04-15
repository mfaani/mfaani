---
title: "CurrentValueSubject Example"
date: 2022-04-14T15:20:02-04:00
categories: [Combine]
tags: ['swift', 'currentValueSubject', 'combine']
---

I googled a bit for "CurrentValueSubject Example". Surprisingly I wasn't able to find a simple answer. I created a few examples:

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
debug - : receive subscription: (CurrentValueSubject)
debug - : request unlimited
debug - : receive value: (Jason)
Jason
debug - : receive value: (Jason Bourne)
Jason Bourne
debug - : receive cancel
```

## Deinitialization gotcha 1

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
        let _ = name.print("debug - ").sink { value in
            print(value) // Jason. WILL NOT necessarily be called for 'Jason Bourne'
        }
        name.send("Jason Bourne")
    }
}
```

Here the subscription is valid only until the end of the `setup` function. However when you use `_` you're telling the compiler that I care less and it may be deinitialized immediately. 

Generally the only way to guarantee your subscriptions stay alive is to reference them from a lexical scope that outlives the time you want to receive subscriptions.


## Deinitialization gotcha 2

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
        let sub = name.print("debug - ").sink { value in
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
        sub = name.print("debug - ").sink { value in
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
print(name) // Combine.CurrentValueSubject<Swift.String, Swift.Never>
print(name.value) // Jason

name = "David" // ❌ ERROR: Cannot assign value of type 'String' to type 'CurrentValueSubject<String, Never>'
name.send("David") ✅
```

## Summary
- Use `send` to update values. 
- Use `print()` like `name.print("a prefix").sink{...}` to see what's happening under the hood.
- Your subscription needs to be retained, otherwise your subscritpions won't happen. 
- The reason a subscription returns an `AnyCancelleable` is to give you control over the scope/duration of the subscription. 
- `AnyCancellable` automatically calls `cancel` when deinitialized.

