---
title: "Iteration Gotchas"
date: 2024-11-15T10:57:16-05:00
draft: true
---

## Don't used `enumerated` for non zero-based indexed collections

```swift
var a = [10,15,20,25,30]


for x in a[2...4].enumerated() { // This collection is not starting from its zero index. So `enumerated` can become faulty. 
    print(x)
}
// (offset: 0, element: 20)
// (offset: 1, element: 25)
// (offset: 2, element: 30)


### Use `zip` instead

for x in zip(a[2...4].indices,a[2...4]) {
    print(x)
}
// (2, 20)
// (3, 25)
// (4, 30)
```


> When you enumerate a collection, the integer part of each pair is a counter for the enumeration, but is not necessarily the index of the paired value. These counters can be used as indices only in instances of **zero-based**, integer-indexed collections, such as Array and ContiguousArray. For other collections the counters may be **out of range** or of the **wrong type** to use as an index. To iterate over the elements of a collection with its indices, use the `zip(_:_:)` function. - {{< rawhtml >}}<small><small><small> <a href="https://developer.apple.com/documentation/swift/array/enumerated()">Apple Docs - enumerated</a></small></small></small>{{< /rawhtml >}}

## Don't do `array.count - 1`

```swift
for i in 0...array.count - 1 {
    array[i]
}
```

That would cause an index out of bounds when the array is empty.  

Alternatives: 

### Relying on your own math
```swift
for i in 0..<array.count {
    array[i]
}
```

 > You're not properly using the language facilities. By doing `< array.count` You're relying on your own math. Avoid relying on your own math.

### Using Swift facilities

```swift
for i in array.indices {
    array[i]
}
```

> Any time you do `collection[i]`, that `i` should be the result of a *method* on collection - Rop Napier

OR

```swift
for item in array {
    item
}
```

If you ever needed to repeatedly get a hold of a range on a collection, and the collection kept on shrinking then try using 