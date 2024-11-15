---
title: "Iteration Gotchas"
date: 2024-11-15T10:57:16-05:00
draft: true
---

## Other complications

```swift
var a = [10,15,20,25,30]


for x in a[2...4].enumerated() {
    print(x)
}

for x in zip(a[2...4].indices,a[2...4]) {
    print(x)
}
```


> When you enumerate a collection, the integer part of each pair is a counter for the enumeration, but is not necessarily the index of the paired value. These counters can be used as indices only in instances of **zero-based**, integer-indexed collections, such as Array and ContiguousArray. For other collections the counters may be **out of range** or of the **wrong type** to use as an index. To iterate over the elements of a collection with its indices, use the `zip(_:_:)` function. - {{< rawhtml >}}<small><small><small> <a href="https://developer.apple.com/documentation/swift/array/enumerated()">Apple Docs - enumerated</a></small></small></small>{{< /rawhtml >}}



```
for i in 0...array.count - 1 {
    print(i)
}
```