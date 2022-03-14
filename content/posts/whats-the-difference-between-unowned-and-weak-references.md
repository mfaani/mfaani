---
title: "Whats the Difference Between Unowned and Weak References?"
date: 2022-03-11T17:30:09-05:00
category: ['iOS', 'Swift']
tags: ['memory-management', 'reference-counting', 'swift']
---

TIL I finally learned when to use `unowned` as opposed to using `weak`.

- `unowned` unowned is essentially a force-unwrap of a weak capture, with all that it entails. Because of this using it sligtly more dangerous. 
- Because it’s not `nil`-able, it’s not optional. 
- Because it's not `nil`-able, the call site becomes cleaner i.e. you don’t have to do the optional unwrapping dance.

### Don't
Use `unowned` for async/network operations. Because the object may become `nil` and then accessing it would crash!

### Do
Only use `unowned` when you’re certain that something can never be `nil` and you just want cleaner looking code.
e.g. you need to break the reference cycle between a childVC & ParentVC. Obviously a childVC can’t be on the stack if its parent is `nil`. So that’s a good example.

**Note:** You could use `weak`, which then you'd have to do optional unwrapping. However it's just not as clean as using `unowned`...

### Docs
The [docs](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html) say: 

> In contrast, use an `unowned` reference when the other instance has the _same_ lifetime or a _longer_ lifetime

> Unlike a `weak` reference, an `unowned` reference is expected to always have a value. As a result, marking a value as `unowned` doesn’t make it optional, and ARC **never** sets an `unowned` reference’s value to `nil`.


### Example (from Apple): 

```swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number`
        self.customer = customer
    }
    deinit { print("Card #\(number) is being deinitialized") }
}
```

> Because a credit card will **always** have a customer, you define its customer property as an `unowned` reference, to avoid a strong reference cycle.

### Hows does the crash look like? 

```swift
class ViewController: UIViewController {
    var original: UIViewController?
    unowned var unowned: UIViewController = UIViewController() // doing this just because we need to initialize all non-optional properties...
    
    override func viewDidLoad() {
        super.viewDidLoad()
        original = UIViewController()
        unowned = original!
        
        original = nil
        print(unowned) // Thread 1: signal SIGABRT --- Fatal error: Attempted to read an unowned reference but the object was already deallocated
        print(original!) // Fatal error: Unexpectedly found nil while unwrapping an Optional value
    }
}
```

A crash for an `unowned` reference that's been deallocated is:

> signal SIGABRT --- Fatal error: Attempted to read an `unowned` reference but the object was already deallocated


### References:
- https://stackoverflow.com/questions/54299046/what-means-unowned-self-and-what-are-the-benefits/54313373#54313373
- https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html