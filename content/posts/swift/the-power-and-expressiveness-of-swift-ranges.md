---
title: "The power and expressiveness of Swift ranges"
date: 2023-03-10T09:51:10-05:00
category: "swift"
tags: ["swift ranges", "ranges in swift", "closedrange", "partialrange", "interviewing"]

---

Ranges in swift are super simple to create. Yet they come in various forms. 

```swift
let r1 = 1...3
let r2 = 1..<3
let r3 = ...3
let r4 = 3...
let r5 = ..<3
```

They all have range like characteristics, but have slightly different traits. It's because they're actually different types.

## Different Range Types
```swift
let r1 = 1...3 // ClosedRange<Int>
let r2 = 1..<3 // Range<Int>
let r3 = ...3  // PartialRangeThrough<Int>
let r4 = 3...  // PartialRangeFrom<Int>
let r5 = ..<3  // PartialRangeUpTo<Int>
```

By having different types, the compiler can enforce certain behavior/restrictions within each type. Looking at the names without seeing the syntax that generates them can be confusing. Hopefully the code above helps with that. 

- `ClosedRange`: Contains both lower bound and upper bound. 
- `Range`: Contains the lower bound, but not the upper bound.
- `PartialRangeThrough`: A partial interval _up to, and including_, an upper bound.
- `PartialRangeFrom`: A partial interval extending upward _from_ a lower bound.
- `PartialRangeUpTo`: A partial half-open interval _up to, but not including_, an upper bound.

Each type exposes a slightly different set of properties. 

```swift
let r3 = ...3 // PartialRangeThrough<Int>
r3.lowerbound // doesn't compile
r3.upperbound // compiles
```

```swift
let r4 = 3... // PartialRangeFrom<Int>
r4.lowerbound // compiles
r4.upperbound // doesn't compile
```

```swift
let r5 = ..<3 // PartialRangeUpTo<Int>
r5.lowerbound // doesn't compile
r5.upperbound // compiles
```

tldr if a range doesn't really have a lowerbound / upperbound, then the compiler doesn't allow accessing it either.

## Range Usage

### Iterating over a specific range within a sequence:

```swift
let range = 1...3
let nums = [8,9,10,11,12]
for i in nums[range] {
    print(i)
}
// Output: 9,10,11
```


### Iterating over a range itself:

```swift
for i in r1 {
    print(i)
}
// Output: 1,2,3
```

### Creating an Array: 

```swift
let array = Array(1...5)
print(array) // [0, 1, 2, 3, 4, 5]
```

## Syntax

Ranges are created using either the [`...`](https://developer.apple.com/documentation/swift/comparable/'...(_:_:)) operator or [`..<`](https://developer.apple.com/documentation/swift/comparable/'.._(_:_:)). Both have a condition that `minimum <= maximum`


Some of their capabilities are: 

- Intersection with another range
- Overlaps with another range
- Bounds (You may not have both upper and lower bounds. It just depends)
- Contains

None existing operators:

```swift
// let c = 1<.. // ERROR
// let y = 1<..11 // ERROR
```
 
## Note the importance of parenthesis: 
I got bit by this a number of times, so I thought I mention this: 
```
let points = [3,5,2]
[..<points.count - 1] // incorrect. [range - 1] has no meaning
[..<(points.count - 1)] // correct
```

```
let (x,y) = (2,10)
x...y.forEach // incorrect. 
(x...y).forEach // correct
```

## Are the following the same? 

```swift
let closedRange = Int.min...3
let partialRange = ...3
```

Best way is to try them out

```
let numbers = [10, 20, 30, 40, 50, 60, 70]

print(numbers[closedRange]) // 💣💣💣 ERROR: Negative Array index is out of range
```
It's because the range starts from index: -9223372036854775808 (Int.min) all the way to index: 3

```
let numbers = [10, 20, 30, 40, 50, 60, 70]

print(numbers[partialRange]) // from the first index in the sequence, all the way to index: 3 
// Prints "[40, 50, 60, 70]"
```

`print(numbers[3...])` doesn't translate to `from index:3` to `index: Int.max`. It translates to "I want everything after this point. In this everything after (and including) 3"

Basically the start and end are implicit here. It's based on the collection they are _applied on_. Not the range of the index type.


## What do docs say in regards to ranges that don't have limits?

> It is safe to use operations that put an upper limit on the number of elements they access.

To be more accurate, the [docs](https://developer.apple.com/documentation/swift/partialrangefrom) say (in full):

> Because a `PartialRangeFrom` sequence counts upward indefinitely, do not use one with methods that read the entire sequence before returning, such as `map(_:)`, `filter(_:)`, or `suffix(_:)`. It is safe to use operations that put an upper limit on the number of elements they access, such as `prefix(_:)` or `dropFirst(_:)`, and operations that you can guarantee will terminate, such as passing a closure you know will eventually return true to `first(where:)`.

Meaning the following have to process the **entire** range, before the application of `map` or `filter` finishes. 

```swift
(1...).map { $0 * 2 } // 💣💥
(1...).filter { $0 % 2 == 0 } // 💣💥
```

However the following is ok, because it's immediately bounded by the array's bounds. 
```
let arr = [1,2,3][1...].map { $0 * 2 }
print(arr)

let arr2 = [1,2,3][1...].filter { $0 % 2 == 0 } 
print(arr2)
```


Conversely in the case of `dropFirst` it only has to adjust a finite number of items in the range. 

```
(1...).dropFirst(5) // good
```
From docs again 
> The behavior of incrementing indefinitely is determined by the type of Bound. For example, iterating over an instance of PartialRangeFrom<Int> traps when the sequence’s next value would be above `Int.max`.


## Where does the knowledge of ranges become useful? 
I found it to be extremely useful during [Advent of Code](https://adventofcode.com) code challenges. I think it's also very useful for Leetcoding and Interviewing. Like instead of trying to manually skip certain bounds within an array, I just modify the range I need to access within an array. 

> Ranges help you filter the elements before you get into the for loop - Myself

```
for (i,v) in [1,2,3,4,5] {
    if i => 3 {
        return 
    }
}
```

vs

```swift
for v in [1,2,3,4,5][..<4] {
    print(v)
}

for v in [1,2,3,4,5][newLowerBound..<newUpperBound] {
    print(v)
}
```


## Can I create ranges for stuff other than numbers? 
Yes!

> Use the closed range operator (...) to create a closed range of any type that conforms to the **Comparable** protocol. 

### Character Range
Like I've seen folks do 
```swift
let alphabets = "abcdefghijk"
```
However you can simply do: 
```swift
let alphabets = "a"..."k"
```

### String Range
You could create ranges for Strings. Think of them as two words in a dictionary (like a real book dictionary)

```swift
let r = "alpha"..."clpha"

["alphaa","d", "cmllllll", "cllzzzz"].forEach {
    print(r.contains($0)) // true, false, false, true
}
```

For String it's basically a lexicographically i.e. a dictionary sort.


### Custom Type Range
```swift
struct Person: Comparable {
    let name: String
    let salary: Int
    
    static func < (lhs: Person, rhs: Person) -> Bool {
        return lhs.salary < rhs.salary
    }
}

let range = Person(name: "Mohammad", salary: 10000)...Person(name: "Matt", salary: 20000) // Named After Matt Smollinger who's mentored me through out my career + answered countless questions from me. Currently an EM at Ford.

range.contains(Person(name: "Kotaro", salary: 15000)) // true. Named after Kotaro Fujita who's the co-organizer of PhillyCocoa, Side Project Spot Lot Podcast. Principal Engineer at Comcast and more.
```

Surprisingly I wasn't able to do: 

```swift
let r2 = "a"..."m"
print(r2.count) // Referencing property 'count' on 'ClosedRange' requires that 'String' conform to 'Strideable'
```
Shout out to [Josh Caswell](https://github.com/woolsweater) for helping me figure it out: 

The reason for this error is that there's a conditional conformance for `ClosedRange` when its `Bound` is `Strideable`. See [extension ClosedRange : Sequence where Bound : Strideable](https://github.com/apple/swift/blob/main/stdlib/public/core/ClosedRange.swift#L200-L201)

The Swift Engineers have decided not to not have `Character` conform to `Strideable`. To learn why, see my other post on [Why Can't You Loop Over Ranges of Characters in Swift](http://mfaani.com/posts/swift/why-cant-you-loop-over-ranges-of-characters-in-swift/)

## Any last words?

- Often usage of ranges can be difficult. Because you might need to convert a `Range` into `ClosedRange` or vice versa and it's not very straightforward. 
- You might have to handle bounds. Example if an array is is empty then the `0...array.count - 1` range will translate to `0...-1` which results in a crash/error. Or you might have a left and right range where your range shrinks every time, this could lead to a range of 0, 1, or often negative. So you have to be considerate of all those. As a result you must always have **safety checks** in your ranges, otherwise your app will crash. To avoid that you should always have the following check `if range.startIndex < range.endIndex` before processing values at
- You can create ranges on anything that's `Comparable`. However only for ranges that are `Strideable` you can do things like `count` or `for loop`.
- There's another range type that we didn't discuss. See [unboundedrange](https://developer.apple.com/documentation/swift/unboundedrange_/)
