---
title: "How to Think Recursively - Part 2"
date: 2022-11-15T16:46:46-05:00
description: "How to break down a recursive question into smaller pieces. Sophisticated Question."
category: "Interviewing"
tags: ["Recursion", "Dynamic Programming", "Algorithmic Thinking", "Generate Well-Formed Parenthesis"]
---

Please read [How to Think Recursively](http://mfaani.com/posts/interviewing/how-to-think-recursively-part1/) before reading this post. 

This post re-applies the steps mentioned in the previous post on a more challenging question.

## Question
Return all possible ways we can generate a well-formed parenthesis?

Examples:
- if `n = 1` then we can only form `()`
- if `n = 2` then we can form `(())` and `()()`
- if `n = 3` then we can form `((()))`, `(())()`, `()(())`, `(()())`, `(), (), ()`


Let's try applying our 4 steps: 
### Summary of steps
1. [What information do I need to pass down for each path](http:mfaani.com/posts/interviewing/how-to-think-recursively-part1/#ask-yourself-what-information-do-i-need-to-pass-down-for-each-path-so-i-can-have-all-the-variables-needed-to-make-a-decision): The total number of opens, the total number of closed or perhaps how many more have we opened vs closed. 
2. [Under what conditions do I stop tree traversal?](http:mfaani.com/posts/interviewing/how-to-think-recursively-part1/#under-what-conditions-do-i-stop-tree-traversal-what-do-i-return-or-do-in-case-of-a-void-function): If I've opened more parenthesis than our target. If I've closed more than we've opened. If I've closed more than our target. 
3. [So I didn't hit a base case. What then?](http:mfaani.com/posts/interviewing/how-to-think-recursively-part1/#so-i-didnt-hit-a-base-case-what-then): Recursively call the function. Bifurcate into opening and closing both. Don't be smart: all code-paths at this point should call your function again. Let it exit any of its base cases in the next function execution.
4. [Figure how to make the first recursive call](http:mfaani.com/posts/interviewing/how-to-think-recursively-part1/#figure-out-how-to-call-your-recursive-function-from-your-main-function): Well we start from `0` open/close parenthesis.

```swift { hl_lines=["19-21"]}
enum P: String {
    case open = "("
    case close = ")"
}
/// - Parameters
///   - diff: number of opened parenthesis subtracted by the number of close parenthesis
///   - openedCount: We need this to be sure we don't exceed the total number of allowed parentheses
func generateParen(num: Int) -> [String] {
    var ans: [String] = []
    func h(diff: Int, paren: P, openedCount: Int, currentPath: String) {
        
        if diff == 0 && openedCount == num {
            ans.append(currentPath)
        } else if diff < 0 {
            // we've closed more than we've opened
        } else if diff > num {
            // we've opened parenthesis more than our num
        } else {
            if openedCount < num {
                h(diff: diff + 1, paren: .open, openedCount: openedCount + 1, currentPath: currentPath + P.open.rawValue)
            }
            h(diff: diff - 1, paren: .close, openedCount: openedCount, currentPath: currentPath + P.close.rawValue)
        }
    }
    h(diff: 1, paren: .open, openedCount: 1, currentPath: "(")
    return ans
}

print(generateParen(num: 3)) // ["((()))", "(()())", "(())()", "()(())", "()()()"]

```

So the above is good. It's correct. However we didn't follow one of our principles. Can you guess?

The highlighted lines is only executed if `openedCount < num`. But it's cleaner if we just allowed it to be called, but handled it in the base case *exits*. 

## Cleaner solution
In our `else`, we're just calling the recursive function. We don't have any conditions. We haven't sneaked in any base-case handling into there. 
All our base cases are moved to the beginning of the function. 

This has two advantages: 
- Groups all the bases cases together. This makes it a lot easier to process logic.
- Reduces indentation from our code. 

```swift { hl_lines=["16-19"]}
enum P: String {
    case open = "("
    case close = ")"
}
func generateParen(num: Int) -> [String] {
    var ans: [String] = []
    /// - Parameters
    ///   - remainingOpen: number of parenthesis that aren't closed.
    ///   - openedCount: We need this to be sure we don't exceed the total number of allowed parentheses
    func h(remainingOpen: Int, paren: P, openedCount: Int, currentPath: String) {
        
        if remainingOpen == 0 && openedCount == num {
            ans.append(currentPath)
        } else if remainingOpen < 0 {
            // we've closed more than we've opened
        } else if remainingOpen > num {
            // we've opened parenthesis more than our num
        } else if openedCount > num {
            // we've opened parenthesis more than our num
        } else {
            h(remainingOpen: remainingOpen + 1, paren: .open, openedCount: openedCount + 1, currentPath: currentPath + P.open.rawValue)
            h(remainingOpen: remainingOpen - 1, paren: .close, openedCount: openedCount, currentPath: currentPath + P.close.rawValue)
        }
    }
    h(remainingOpen: 1, paren: .open, openedCount: 1, currentPath: "(")
    return ans    
}

print(generateParen(num: 3))
```

In the above, we have three base cases. Followed by recursive calls. It's much cleaner. We clearly isolate base case exits from recursive function calls. To say things differently: It's fine to have if-else to recurse differently. But don't have `if` conditions without `else` where it limits the recursion. Because the lack of recursion within an else implies an early exit condition

Because we added this cleanliness, we can identify something that can be improved. 

The highlighted lines have overlapping logic. We can combine them into `if openedCount > num`. That's because the `openedCount > num` is enough of a limiting factor and it won't ever allow us to go beyond the max open. 

> The other to think about this is the number of conditions needed. We only need to make sure: 
> - We don't open more than the expected count.
> - We don't close more than we've opened.
>
> Two conditions should require only two `if` clauses...

## Cleanest Solution - removing extra check

```swift
enum P: String {
    case open = "("
    case close = ")"
}
func countParan(num: Int) -> [String] {
    var ans: [String] = []
    
    func h(remainingOpen: Int, paren: P, openedCount: Int, currentPath: String) {
        
        if remainingOpen == 0 && openedCount == num {
            ans.append(currentPath)
        } else if remainingOpen < 0 {
            // we've closed more than we've opened
        } else if openedCount > num {
            // we've opened parenthesis more than our num
        } else {
            h(remainingOpen: remainingOpen + 1, paren: .open, openedCount: openedCount + 1, currentPath: currentPath + P.open.rawValue)
            h(remainingOpen: remainingOpen - 1, paren: .close, openedCount: openedCount, currentPath: currentPath + P.close.rawValue)
        }
    }
    h(remainingOpen: 1, paren: .open, openedCount: 1, currentPath: "(")
    return ans    
}

print(generateParen(num: 3))
```