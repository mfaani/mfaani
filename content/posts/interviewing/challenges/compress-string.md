---
title: "Compress String"
date: 2022-06-15T08:27:30-04:00
category: "interviewing"
tags: ["string", "string-compression", "swift"]
description: "Most simple compress algorithm"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

### Question

A simple compression algorithm would be to replace repeating characters with their count. 

### Example

```
aaa --> a3
aaabb --> a3b2
aaabbaa --> a3b2a2
```

### Want to stand out? 

Ask if there's a difference between `a` & `A` and if you need to add uppercase & lowercase together or need to separate them. 

In my implementation I assumed they're different. 

### Code

```swift
// start counting, then upon seeing a diff, write the count.

func compress(_ str: String) -> String { 
    let chars = Array(str)
    guard !chars.isEmpty else { return str }

    var currentChar = chars.first!
    var currentCount = 1
    var ans: String = ""
    guard chars.count > 1 else { return "\(currentChar)\(currentCount)"}
    
    for i in 1..<chars.count { 
        if chars[i] == currentChar {
            // increment
            currentCount += 1
        } else {
            // append subsection
            ans.append("\(currentChar)\(currentCount)")
            // reset
            currentChar = chars[i]
            currentCount = 1
        }
    }
    ans.append("\(currentChar)\(currentCount)")
    return ans
}

```

### Tests

```
print(compress("aaabbbbccaA")) // a3b4c2a1A1
print(compress("a")) // a1
print(compress("")) // 
```