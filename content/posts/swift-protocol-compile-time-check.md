---
title: "Swift Protocol Compile Time Check"
date: 2021-12-30T14:25:11-05:00
slug: "swift-protocol-compile-time-check"
tags: ['swift', 'swift-protocols', 'compile-time-check', 'misconception']
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

I always thought that if you just use `{get}` on a protocol variable, then you can still set it i.e. it doesn't matter if you give it a setter or not. That's not true. It really depends on which compiler checks come in to place. Compiler checks are different depending on the _type_ you want a variable to be. 

Can you guess which of the two snippets will compile? 

**Snippet A**

```swift
protocol Person {
    var age: Int { get }
}

class Adult: Person { 
    var age = 20
}

let a = Adult()
a.age = 20
```

**Snippet B**

```swift
protocol Person {
    var age: Int { get }
}

class Adult: Person { 
    var age = 10
}

let a: Person = Adult()
a.age = 20
```

Only snippet A compiles. 

Snippet B gives the following error: 

> Cannot assign to property: 'age' is a get-only property


### Why?

In snippet A, when we write down `let a = Adult()` the compiler looks at the definition of `Adult.age` and sees it's _mutable_ so it allows it. 

 Where in snippet B we have `let a: Person = Adult()`, the compiler looks at the definition of `Person.age` and sees it's an _immutable_ property. So it disallows that modification. 

### Summary

If the type that the compiler has to check against is the Protocol type itsef, then the getter/setter used on a protocol must be adhered exactly...

The compiler requires that you satisfy protocol requirements. As long as the class that adopts a protocol, conforms to its requirements then the compiler is happy. Yet the compiler will not assume the type of a variable to be its assignment type. It just so happens that the 'assignment type' satisfies the 'declared type'. 

To understand the jargon used: 

```swift
var x: SomeType = SomeClass()
/*          |              | 
      declared type   assignment type
*/
```

### Credits

Special thanks to [Suyash Srijan](https://twitter.com/suyashsrijan) who helped me figure this out.