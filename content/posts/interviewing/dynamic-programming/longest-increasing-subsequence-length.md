---
title: "Longest Increasing Subsequence Length"
date: 2022-08-25T07:50:38-04:00
category: "interviewing"
tags: ["swift", "dynamic-programming", "longest-increasing-subsequence", "subsequence"]
description: "Learn how to calculate the longest increasing subsequence"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---
Before we present the question. Let's figure out what a subsequence is:

### What's a subsequence?
Any selection of items from the original array. The selection must respect the order. Meaning for `[1,2,3,4,5]` only two of the four below are subsequences:

```swift
[1,2,3,4,5] ✅
[1,4,3,2,5] ❌ order not respected
[1,2,5] ✅
[5,1] ❌ order not respected
```

#### What's the difference between a subsequence and a subarray?
Subarray, is just like subsequence, that is order must be respected. Additionally the sub-array has to be made of continuous elements. 

```swift
[1,2,3,4,5] ✅
[1,2,3,5] ❌ gap 
[1,2,3] ✅
[2,4] ❌ gap
```

### What's a longest increasing subsequence? 
This is also known as LIS.  
For `[0, 3, 1, 7, 5, 2, 8]` the longest _increasing_ subsequence is: `[0,1,7,8]`.  
For `[2,2,2,2]`. LIS is: `[2]`.  
For `[3]`. LIS is: `[3]`

In this post, we're only going to calculate the _length_ of the LIS. We won't construct the actual subsequence itself. To learn how to construct the path, see [here](https://stackoverflow.com/questions/14806328/how-to-print-longest-increasing-subsequencelis-from-a-given-array)

### Explanation
So this problem can be broken into subproblems i.e. 
- The LIS of index 6, depends on the LIS of all indices between 0-5. 
- The LIS of index 5, depends on the LIS of all indices between 0-4.
- ...
- The LIS of index 1, depends on the LIS of index 0
- The LIS of index 0, is 1. Because every element has a subsequence of 1. 

Whenever we can turn our problem into subproblems, then Dynamic programming is a good candidate for finding a solution.  
To do this, let's create an array named `dp` to represent the the answer to the subproblem for a given index.

`let arr = [0, 3, 1, 7, 5, 2, 8]`
We'll create an array with the same length of our original array. Default all the values to `1`. Example: `var dp: [Int] = Array(repeating: 1, count: arr.count)`.  
Let's just assume we want to calculate the LIS all the way to index `4`. And we've already updated the value for all previous indexes.  How do you think we need to update `dp[4]`:

```
    [0, 3, 1, 7, 5, 2, 8]
                  ↑
                dp[4]
```

Which of the following would it be?
1. The value for `dp[4]` will be `max(dp[0] + 1, dp[1] + 1, dp[2] + 1, dp[3] + 1)`
2. The value for `dp[4]` will be `max(dp[0] + 1, dp[1] + 1, dp[2] + 1)`


The correct answer is the second line.  
First line is incorrect because `7` is bigger than `5`. `dp[3] + 1` shouldn't be considered.  
This means that assuming there is at least one item in the array, the default value should then be `1`.

In short:
- For every index, we use compare its value with its previous indices
    - If current index is bigger, then we increase the LIS length between the two indices.
    - At any given index we perform a `max` between all nodes that were able to increase the subsequence to that point. 
- We do this till the end of the array.  
- Then do a max against our dp array.

#### Pro tip
Use a **paper** and go through this example again by yourself.
Just compare any two indexes, add to the previous, do a max. 
I was then able to reason with it a lot lot easier on paper.
Here's what I wrote: 

!["LIS steps"](/LIS.jpg "Longest Increasing Subsequence Length")

#### Code


```
func lengthOfLIS(_ nums: [Int]) -> Int {
    var dp: [Int] = Array(repeating: 1, count: nums.count)
    
    for i in 0...nums.count - 1 {
        for j in 0..<i {
            if nums[j] < nums[i] {
                dp[i] = max(dp[i], dp[j] + 1)
            }
        }
    }
    
    return dp.max()!
}
```
Most of the time when you want to memoize things you use a dictionary. We could certainly do that here too. Here in this example, I used an array, because the [video](https://www.youtube.com/watch?v=cjWnW0hdF1Y) I saw was using an Array :). It intiially put me off, but I get it now.  
Like if all you need to do is add and access certain indexes and not search or remove items, then array and dictinary aren't different so much. 

Additionally in in this question, going from left to right or right to left don't make a difference in terms of our answer. 

- If we start from the beginning, then each index will get added to all answers of its previous indicides — if it's bigger. 
- If we start from the end, then each index will still get added to all answers of its next indicies — if it's bigger.

It really doens't matter which way we select. 


### Other Questions that use LIS:
[Russian Doll - Envelopes](https://leetcode.com/problems/russian-doll-envelopes/). I plan on posting about it next. 