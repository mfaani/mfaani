---
title: "The effect of direction on recursion and understanding code"
date: 2023-12-02T17:34:13-05:00
category: interviewing
tags: [swift, dynamic-programming, tabulation, memoization, recursion, two-pointers, longest common sequence, LCS]
description: "Solve a fun challenged named 'Longest Common Subsequence' by learning how the direction in recursion can effect your perception but also make comparing code difficult"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

Today I'm going to discuss another fun and common challenge. It's the [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence) a.k.a. LCS.

I'll first focus on discussing a pain point I went through when I was trying to compare the algoritm I deduced on my own vs a few other algorithms I saw online. Our algoritms seemed very similar. Yet different. It made debugging my code based on other code very difficult. This is a very commong problem I face when I doing leetcode. For whatever reason my way of coding is more than often to a good extent different from what I see from others. I find it easier to compare the high level of my code with a video from Youtube since most other submissions on Leetcode itself don't contain a good breakdown pseudocode of what's happening. 

Let's first explain the problem. Suppose we have: "dabc" and "aafb". The longest common sequence is "ab". Other examples:

| Text1  | Text2  |  LCS  |
| ------ | ------ | ----- |
| "abc"  | "abc"  | "abc" |
| "dab"  | "db    | "db"  |
| "eaf"  | "abeaf"| "eaf" |
| "kpm"  | "qspzm"| "pm"  |


The way to do this is to have **two pointers**. One pointer for the current index you're trying to match for `text1`, another for `text2`. Each location in the grid below corresponds to the two pointers. Example:

text1: kpm  
text2: qspzm

### Approach One

```
       0   1   2    
       k   p   m    
0  q   🏁  x   x  🔚 
1  s   x   x   x  🔚
2  p   x   x   x  🔚
3  z   x   x   x  🔚
4  m   x   x   🎯 🔚
       🔚 🔚  🔚
```      

{{< rawhtml >}}
<sub>
🏁: start</br>
🎯: end</br>
🔚: out of bounds. return 0</br></br>
</sub>
{{< /rawhtml >}}



Start from `(0,0)` and finish at bottom right corner.  
return `0` for any value that's beyond the _right_ or _top_ edges of the grid.

---

### Approach Two

```
           0   1   2    
           k   p   m
           🔚  🔚  🔚    
0  q  🔚   🎯  x   x
1  s  🔚   x   x   x
2  p  🔚   x   x   x
3  z  🔚   x   x   x
4  m  🔚   x   x   🏁
```

Start from `(2,4)` and finish at top left corner.  
return `0` for any value that's beyond the _left_ or _bottom_ edges of the grid.

> My confusion originated from the fact that I was coding from one approach, but trying to fix my code based on another approach — without realizing the approaches are different! 🙃🫤🫤😐🤔

## Approach One Code

```swift
/// Starts from 0,0. Ends at m * n
/// Out of bounds would be the BOTTOM and RIGHT edges.
func longestCommonSubsequence(_ text1: String, _ text2: String) -> Int {
    var t1: [Character] = Array(text1)
    var t2: [Character] = Array(text2)

    func helper(s: Stage) -> Int {
        guard s.s1 < t1.count && s.s2 < t2.count else {
            return 0
        }
        if t1[s.s1] == t2[s.s2] {
            return helper(s: Stage(s.s1 + 1, s.s2 + 1)) + 1
        }
        return max(helper(s: Stage(s.s1 + 1, s.s2)), helper(s: Stage(s.s1, s.s2 + 1)))
    }

    return helper(s: Stage(0, 0))
}

struct Stage: Hashable {
    var s1: Int
    var s2: Int
    
    init(_ s1: Int, _ s2: Int) {
        self.s1 = s1
        self.s2 = s2
    }
}
```

## Approach Two Code

```swift
/// Starts from m * n. Ends at 0 * 0
/// Out of bounds would be the TOP and LEFT edges. 
func longestCommonSubsequence(_ text1: String, _ text2: String) -> Int {
    var t1: [Character] = Array(text1)
    var t2: [Character] = Array(text2)
    
    func helper(_ s: Stage) -> Int {
        
        guard s.s1 >= 0 && s.s2 >= 0 else {
            return 0
        }

        if t1[s.s1] == t2[s.s2] {
            return helper(Stage(s.s1 - 1, s.s2 - 1)) + 1 
        } else {
            return max(helper(Stage(s.s1 - 1, s.s2)), helper(Stage(s.s1, s.s2 - 1)))
        }
    }
    return helper(Stage(t1.count - 1, t2.count - 1))
}
   
```

## Summary and some other confusions I had along the way 
To figure out the issue with my code I thought I had look up 'top down vs 'bottom up' & 'memoization vs tabulation'. But while those are unrelated to figuring out this issue and might confuse you even more, unless fully understood, understanding their differences might make it easier for you to compare different approaches. Try not to spend too much time on the differences. Instead focus on learning how to break down your problem into a simple subproblem.

About Top down approach, [Bard](https://bard.google.com/chat/f770e0be6d4da48b) said:

> Recursive algorithms are called top-down because they break down a problem into smaller and smaller subproblems until they reach a _base case_, which is a simple problem that can be solved directly. Once the base case is solved, the results are used to solve the larger subproblems, and so on, until the original problem is solved. This process is called recursion, and it is a powerful way to solve many different types of problems.
>
> The term "top-down" is used to describe this approach because the algorithm starts at the top of the problem hierarchy and works its way down to the base cases. This is in contrast to a bottom-up approach, which starts at the base cases and works its way up to the top of the problem hierarchy.

Hence both approaches are top down. The first time I heard the term 'top-down', I thought it had something to do with the fact that in tree traversals you start at the root and the root is always (in algorithm world, not real life) at the top of tree. But it has nothing to do with that 🙃.

### Optimizations
For both of these solutions you can do memoization. Tabulation though is more natural when you start from `0,0`
- `tabulation` for when you start from (0,0). See this [gist]((https://gist.github.com/mfaani/0d6a76708d8b8f3bb41bdd13e7c0014e)) for its code 
- `memoization` for when you start from (m,n). See this [gist](https://gist.github.com/mfaani/89069e38f49eea20b7e47b8f14308505) for its code. 

Essentially where you start from will dicate the direction. The direction often happens to also dictate the most performant algorithm you can choose.

> With tabulation you compute subproblems **in advance**, with memoization you compute subproblems **on demand**.

Subproblems for LCS is: "what's the LCS for two strings prefixed at (s1,s2)?" Both my approaches have the same subproblem. But the code for that becomes different because the direction is different. 

### Note on readibility

The returning value for the two approaches are: 

- `return helper(Stage(0, 0))`
- `return helper(Stage(t1.count - 1, t2.count - 1))`

Which one makes more sense?  
The second approach is slightly more readable in this case. Readers might be confused as to why you're returning the value for `Stage(0,0)` and not the last. That said for me personally it was easier to write code that starts from (0,0) and expand.

## Acknowlegements

Special thanks to [Tim Vermeulen](https://github.com/timvermeulen) for answering dozens of my questions so I was able to put this post together and [Maks Verner](https://stackoverflow.com/users/9651461/maks-verver) for writing [this post](https://stackoverflow.com/a/77560704/5175709)