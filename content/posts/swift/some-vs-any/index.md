---
title: "Some vs Any"
date: 2024-07-17T14:26:39-04:00
description: "What's the difference between some and any? Why do they both exist? Which should I prefer using?"
category: ['Swift']
tags: [Swift, Generics, wwdc2022]
---


# What problem do `some` and `any` solve? 

```swift
var x: Equatable = 10 // Error: Use of protocol 'Equatable' as a type must be written 'any Equatable'
var y: Equatable = "10" // Same error
```

It's because the compiler can't know if the _associated type_ of `x`, matches with the associated type of `y`. So it just forbids it. Compiler just doesn't want to be in situation where you'd try something like:

```swift
if x == y { print("Equal") }
```
Although both are `Equatable`. One is an `Int` while the other is a `String`. So the `==` can't be applied between them.

## Similarities

We can get things to compile using `some` or `any`.

```swift
var x: some Equatable = 10
var y: any Equatable = 9
```

## Differences

- `any` is careless for your return types. Allows the following:

```swift
func aa() -> any Equatable { 
    if a > 3 {
        return 10
    } else {
        return "ten"
    }
}
```


- `some` is thoughtful for your return types. Does *not* allow the following:

```swift
func bb() -> some Equatable { 
    if a > 3 {
        return 10
    } else {
        return "ten"
    }
}
```

Instead `some` allows _identical_ return types of `Int` or `String`:

```swift
func cc() -> some Equatable { 
    if a > 3 {
        return 10
    } else {
        return 9
    }
}

func dd() -> some Equatable { 
    if a > 3 {
        return "ten"
    } else {
        return "nine"
    }
}
```

## How does `some` work across multiple returning types? 

Remember with older Swift we had to write: 

```swift
protocol Animal {...}
func feed<T>(_ animal: T) where: T: Animal {
    ...
}
```

It was cumbersome syntax. Well now we can just write that as simple as: 

```swift
func feed(_ animal: some Animal) {
}
```

> This declaration is identical to the previous one, but the unnecessary type parameter list and `where` clause are gone, because we didn't need the expressiveness they provide.
> 
> Writing `some Animal` is more straightforward, because it reduces syntactic noise, and it includes the semantic information about the animal parameter right in the parameter declaration.
>
> For more see [this wwdc moment](https://developer.apple.com/videos/play/wwdc2022/110352/?time=783)

## When should I use `any`? 

Apple recommends us to start with `some` and then only use `any` when you don't want to restrict the underlying variables to have the same type. Example: 

```swift
let specificAnimals: [some Animal] = [Cow(), Cow()] // ❌ won't allow adding `Dog()`
let variedAnimals: [any Animal] = [Cow(), Dog(), Cat()]

```

## Summary

- The impact of `some` is *across* variables. It enforces idnetical types to be returned.
- The impact of `any` is on *a single* variable. It has no enforcing to keep returning types identical.

!["Swift some vs Any"](some-vs-any.png "WWDC 2022 - Embrace Swift Generics - Holly Borla")

The word choice of `any` vs. `some` is a confusing one. In my humble opinion `any` is named fairly correct, while `some` should have been renamed to either: 
- `anyButMatchingOtherReturningTypes`
- `anyHomogeneousType`
- `anyConsistentType`
- `anyWhileGuaranteeingTypeRelationship_and_HoldingAFixedConcreteType`
- `somy`

I specicially left Apple's jargon of "opaque", "existential", "boxed" out of this conversation as they often make it hard to understand. 

For a more detailed analysis of their differences see [Embrace Swift Generics - WWDC 2022](https://developer.apple.com/wwdc22/110352)