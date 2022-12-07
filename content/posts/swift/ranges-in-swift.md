---
title: "Ranges in Swift"
date: 2022-12-04T18:51:10-05:00
draft: true
category: "swift"
tags: ["swift ranges", "ranges in swift", "closedrange", "partialrange", "interviewing"]

---

Ranges in swift are super simple to create. Yet they vary. 

```swift
let r1 = 1...3
let r2 = 1..<3
let r3 = ...3
let r4 = 3...
let r5 = ..<3
```

Now they all have range like characteristics, but have slightly different traits. 
First and foremost. They're of different types.

# Different Range Types
```swift
let r1 = 1...3 //ClosedRange<Int>
let r2 = 1..<3 //Range<Int>
let r3 = ...3 // PartialRangeThrough<Int>
let r4 = 3... // PartialRangeFrom<Int>
let r5 = ..<3 // PartialRangeUpTo<Int>
```

By having different types, the compiler can enforce certain behavior within each time. 

- ClosedRange: contains both lower bound and upper bound. 
- Range: Contains the lower bound, but not the upper bound.
- PartialRangeThrough: A partial interval –up to, and including_, an upper bound.
- PartialRangeFrom: A partial interval extending upward _from_ a lower bound.
- PartialRangeUpTo: A partial half-open interval _up to, but not including_, an upper bound.

```swift
let r3 = ...3 // PartialRangeThrough<Int>
r3.lowerbound // NOT AVAILABLE
r3.upperbound // available
```

```swift
let r4 = 3... // PartialRangeFrom<Int>
r4.lowerbound // Available
r4.upperbound // NOT AVAILABLE
```

```swift
let r5 = ..<3 // PartialRangeUpTo<Int>
r5.lowerbound // NOT AVAILABLE
r5.upperbound // Available
```

tldr if a range doesn't really have a lowerbound / upperbound, then the syntax doesn't allow accessing it either.
Range usage - iterating over a :

```swift
let nums = [1,2,3,4,5,6,7]
for i in nums[r1] {
    print(i)
}
```
Output: 2,3,4

## Usage
Range usage - iterating over a range:

```swift
for i in r1 {
    print(i)
}
```

None existing operators:

```swift
// let c = 1<.. // ERROR
// let y = 1<..11 // ERROR
```

# Syntax

Don't think of `..` + `>` as two distinct operators. `..>` all together is an operator just like how `...`, `..<`

## Question 

Are the following the same? 

```swift
let or = ...2
let cr = Int.min...2
```

There's a type that expresses a concept of an open-ended range. 
There's also a type that expresses a closed range.
There's value in having these types.

As mentioned earlier `or` only has an upper bound. While `cr` has lower and upper bounds.

```
let numbers = [10, 20, 30, 40, 50, 60, 70]
print(numbers[3...]) // it doesn't translate to from index:3 to index: Int.max
// Prints "[40, 50, 60, 70]"
```

`print(numbers[3...])` doesn't translate to `from index:3` to `index: Int.max`. It translates to "I want everything after this point. In this everything after (and including) 3"

Basically the start and end are implicit here. It's based on the collection they are _applied on_. Not the range of the index type.

Docs say: 

> It is safe to use operations that put an upper limit on the number of elements they access.

To be more accurate, the docs say (in full):

> Because a `PartialRangeFrom` sequence counts upward indefinitely, do not use one with methods that read the entire sequence before returning, such as `map(_:)`, `filter(_:)`, or `suffix(_:)`. It is safe to use operations that put an upper limit on the number of elements they access, such as `prefix(_:)` or `dropFirst(_:)`, and operations that you can guarantee will terminate, such as passing a closure you know will eventually return true to `first(where:)`.

Meaning don't do: 

```swift
(1...).map { ... }
(1...).filter { $0 % 2 == 0 }.forEach { print($0)}
(1...).dropFirst(5).forEach { print($0) }  // The docs say this is good. It's still bad. Because it's unbounded.  
```

Because it will never end. 

> The behavior of incrementing indefinitely is determined by the type of Bound. For example, iterating over an instance of PartialRangeFrom<Int> traps when the sequence’s next value would be above `Int.max`.

Instead do: 

```swift
(1...12).map { ... }
(1...134).filter { $0 % 2 == 0 }.forEach { print($0)}

(1...).prefix(5).forEach { print($0) } // 1,2,3,4,5

```
