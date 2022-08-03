---
title: "Which Way Am I Sorting?"
date: 2022-08-03T08:19:26-04:00
description: "Ever confused as to what the end result of your sorting is?"
category: "interviewing"
tags: ["arrays", "foundations", "swift"]
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

I always got confused as to what's the end result my sort. I wasn't sure if it would end up being ascending or descending. 
The ultimate trick is to not think of **up** vs **down**. Instead think of increasing/decreasing from **left to right**.  
We perceive arrays as _horizontal_ beings. Hence _left and right_ make more sense vs _up and down_


```swift
let nums = [1,4,2,3]
let sorted_nums = arr.sorted(by: {
    $0 < $1 // left is smaller  [1,2,3,4] i.e. ascending
})
```
If you see `$0 < $1` then _left_ side is smaller than the next item. i.e. it's ascending.  


```swift
let nums = [1,4,2,3]
let sorted_nums = arr.sorted(by: {
    $0 > $1  // right is bigger [4,3,2,1] i.e. descending
})
```
If you see `$0 > $1` then _right_ side is smaller than the next item. i.e. it's descending. 
Similarly writing `array.sorted(by: <)` is identical. 

#### Alternate way:
- `array.sorted(by: <)` will make it decreasing.
- `array.sorted(by: >)` will make it increasing.


#### And if I don't pass a block?
It will make the array increasing. It feels natural...

```swift
let nums = [1,4,2,3]
print(nums.sorted()) // [1,2,3,4]
```

