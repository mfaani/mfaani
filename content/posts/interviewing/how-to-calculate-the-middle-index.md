---
title: "How to Calculate the Middle Index?"
date: 2022-06-19T22:55:27-04:00
category: "interviewing"
tags: ["arrays", "foundations", "swift"]
description: "Are middle indexes always actually the middle index?"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

A good number of interview questions require you to constantly split an array/string in half.  
This is relatively easy to achieve when the array count is odd.  
However when the count is even, it's not as easy. 

```
let a = [1,3,8,10,22] // middle index is 2
```

```
let b = [1,3,8,10] // middle index is 1.5 which is non-existent. So what now?
```

Most important thing to note is: There's no such thing as "middle index" when the count is even, I mean there's two middles in that case. I guess the "middle" are those two indices.‌‌ ‌But then you have to pick one. For this reason it's often better to think of it as a **pivot** because it's really the 2nd index. The 2nd index isn't the middle index of a 4 element array. 

Moving away from 'find the middle index' to a 'find the pivot index which we'll use as a middle index' helped me understand the concept better.

After that, you typically just need logic that handles all three cases of before, after and the index itself or similarly lower, greater, equal to the index itself.  

### Example - Flip an array

```
[0,0,2,2] -> [2,2,0,0]
```

The general way to calculate the pivot index is either: 

```
let pivotIndex = (lower + upper) / 2 
let pivotIndex = lower + (upper - lower) / 2 # This approach helps avoid integer overflow. In swift we get the integer number which is automatically rounded down.
```

```
[0,0,2,2]
```

pivot index = (0 + 3) / 2 = 1

```
[0,0,2,2]
   |
 pivot
```

- indexes before pivot: swap 
- indexes equal to pivot: swap (when the range is odd, this would be a no op)
- indexes after pivot: ignore, as they were swapped with indexes before pivot.

### Example - Binary Search

#### Question - Find 99 in the following array
```
[2, 7, 20, 24, 40, 99]
```

#### 1st Iteration
pivot range = 0 -> 5  
pivot index = (0 + 5) / 2 = 2.5 -> pivot is 2

```
[2, 7, 20, 24, 40, 99]
 |     |           |
left  pivot      right
```

#### 2nd Iteration
pivot range = 3 -> 5  
pivot index = 4. It's now actually the middle of its range. 

```
[2, 7, 20, 24, 40, 99]
           |    |   |
        left pivot right
```
#### 3rd Iteration
pivot range = 5 -> 5  
pivot index = 5. left and right index are also index 5

```
[2, 7, 20, 24, 40, 99]
                   | 
                  left
                  pivot
                  right
```

### Different way of seeings things

We're not always cutting the array/section in half. 

- For odd ranges: you calculate the middle index correctly. Yet you’re not precisely cutting the section in half. Example: 

`[2, 7, 20, 24, 40]` pivot point is `index: 2`.  

- You will drop three items: `(20, 24,40)` if you’re looking for `2` or `7`.  
- You will drop three items: `(2, 7, 20)` if you’re looking for `24` or `40`.  
- You will drop four items: `(2, 7, 24, 40)` if you’re looking for `20`.

- For even ranges: You're not really calculating a middle. You **may or may not** end up precisely cutting the section in half. Example: 

`[2, 7, 20, 24, 40, 99]` pivot point is `index: 2`.  

- You will drop four items: `(20, 24, 40, 99)` if you’re looking for `2`, `7`.  
- You will drop three items: `(2, 7, 20)` if you’re looking for `24`, `40`, `99`.  
- You will drop five items: `(2, 7, 24, 40, 99)` if you’re looking for `20`.

👆 isn't to make you think how many you're dropping. It's just once you realize you're not always cutting in half, things actually start to make more sense. 

## Attention
Both 1st and 2nd approach will result in an index 0 for an empty array. Accessing index 0 will result in an "out of bounds" error. So you must exit early for empty arrays. 


## Conclusion

Don't think of middle index as the absolute middle index. Think of it more as a pivot point. 
Things just work as long as you have correct logic to handle all varying cases (less than, equal to, greater than)

### Concluding strategy is: 
- Find the range
- Find a pivot. Don't think of it has a middle index.
- Have appropriate logic to process all the following cases:
  - Before pivot
  - The pivot itself
  - After pivot
- Find new range. Then Find new Pivot. 
- Repeat steps again

### Acknowledgments

Special thanks to [Mira](https://twitter.com/andThenMira), [Suyash](https://twitter.com/suyashsrijan) and [Arthur](https://twitter.com/art_garzajr) for answering my questions so I can put this post together. 
