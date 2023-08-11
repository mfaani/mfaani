---
title: "Check if Two Strings Are One Edit Away"
date: 2022-06-15T06:26:52-04:00
category: "interviewing"
tags: ["interviewing", "string", "string comparison", "swift", "edit distance"]
description: "Learn how to break down a question into smaller pieces"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

### Question
You can edit a string in three different ways: insert, remove or replace a character. Write a function to see if two strings are one or zero edits away. 

### Example
```
like, like  --> true (zero edits)
like, likes --> true (one edit: remove/insert)
like, life  --> true (one edit, replace)
like, lik   --> true (one edit: remove/insert)
like, pine  --> false (two edits)
like, lion  --> false (two edits)
```

### Strategy

1. For most questions that require you to return true or false, you can reduce the scope of the question by removing noise. 
Often that means exiting early or having conditions that make it impossible to have a true result...
Example: If the string _lengths_ differ by 2 or more characters, then you can exit / return an answer. 

2. Try not to solve this problem in one go. Break it down into two checks: 
- Does replacing a character make my strings equal?
- Does inserting a character make my strings equal?

3. Keep things simple. See how I avoid adding complex logic by intentionally passing the longer string as first parameter of `isOneInsertAway`. 

4. Stop caring about things you don't need. You can find the answer without knowing which character, at which index needs to be replaced. A LOT of questions often can be resolved without knowing for a lot of things. Example if you're solving a question on knowing if there's a path between A, B. Then you don't need to care how you got to B. You just need to know if there's a path. Not caring simplifies your thought process. For this reason it's often good to **write down things that don't matter.**

### Code

```swift { hl_lines=[6, 8,10]} 
func isOneOrZeroEditsAway(_ str1: String, _ str2: String) -> Bool { 
    let str1 = Array(str1)
    let str2 = Array(str2)
    
    if str1.count == str2.count {
        return isOneEditReplaceAway(str1, str2)
    } else if str1.count - 1 == str2.count {
        return isOneInsertAway(str1,str2)
    } else if str2.count - 1 == str1.count {
        return isOneInsertAway(str2, str1)
    } else {
        return false // removing noise
    }
}

/// loop over characters. if difference, bump diff count
/// if at the end the count was more than 1, return false. 
func isOneEditReplaceAway(_ str1: [Character], _ str2: [Character]) -> Bool {
    
    var diffCount = 0
    for i in 0..<str1.count {
        if str1[i] != str2[i] {
            diffCount += 1
        }
    }
    return diffCount <= 1
}

/// Order matters. `str1` is always one character longer
/// if character is equal, then proceed
/// if not equal, then just move index on `str1` and continue comparing...
func isOneInsertAway(_ str1: [Character], _ str2: [Character]) -> Bool {
    
    var diffCount = 0 
    for i in 0..<str2.count {
        guard diffCount < 2 else { return false }
        if str1[i + diffCount] != str2[i] {
            diffCount += 1
        }
    }
    return diffCount <= 1
}
```

In the above, in the highlighted lines, we don't care what the edit/remove/replacement was. We just care about its number.

### Tests

```swift
print(isOneOrZeroEditsAway("like", "like")) // true
print(isOneOrZeroEditsAway("like", "likes")) // true
print(isOneOrZeroEditsAway("like", "life")) // true
print(isOneOrZeroEditsAway("like", "lik")) // true
print(isOneOrZeroEditsAway("like", "pine")) // false
print(isOneOrZeroEditsAway("like", "lion")) // false
```

### Complexity

Obviously `O(n)` where `n` is the length of the **shorter** string.

### Related

This question falls under the category of [Edit Distance](https://en.wikipedia.org/wiki/Edit_distance)