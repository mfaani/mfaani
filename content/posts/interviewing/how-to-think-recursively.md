---
title: "How to Think Recursively - Part 1"
date: 2022-10-30T17:19:37-04:00
draft: true
TODO: Add link for ?????
description: "How to break down a recursive question into smaller pieces"
category: "Interviewing"
tags: ["Recursion", "Dynamic Programming", "Algorithmic Thinking"]
---

These article are a bit about the gotchas that I faced. The logic in principle should apply to most recursive problems. 
In this post, I will use the following question as a point of reference:

'Count how many ways you can climb a staircase. You can jump either one step at a time or two steps at a time.'

Example if there are 3 stair cases then you can either jump: 

1,1,1
2,1
1,2

So in total it's 3 ways. To be clear: 

1,1,2 goes beyond the desired stair.
2,2 goes beyond the desired stair as well. 

## Foundational steps
- Know what it means to travel in a DFS vs BFS. Draw diagrams for yourself. If you haven't mastered this, then it may be best to not dive deeper yet. 
    - Try what you've learned with the most simplest examples. Like the staircase example we're using in this post.
    - Understand the term **branching factor**. That means from each node, how many new nodes will become reachable.
        - If all you can do is jump one or jump three, then your branching factor is `2`. 
        - If you were able to only jump six, then your branching factor is just `1`. 

- Try learning about the different classic (but simple) problems that are solved using trees. Just knowing the variant kinds of problems and how they work visually will help you write your own code easier. Knowing the various applications of DFS will help you even if you can't write the code. 
- Use a paper and try to visually solve the question in the simplest form of a tree example (a root and two leafs).
- Then make your tree bigger by adding one more level to your tree and try to visualize the solution again. If you struggle here, then don't go any further until you figure it out. 

## Ask yourself 'what information do I need to pass down for each path, so I can have all the variables needed to make a decision'?
- A tree is basically made up of multiple paths. **Each** path needs to be able to maintain **its own state**. The state can't be shared amongst other paths. If things are shared across paths then a states path gets overridden. You don't want that.
Because of this, the state can't be a property of a class nor a local function of your function. It has to be a argument of the function that you pass down as you traverse the tree. In other words it's the stack's state.
- Here are examples of things you may need to pass down depending on the question: 
    - The _previous actions_ you took, e.g. Jumped 2, then jumped 3. Your helper function should take that as an array of previous jumps `[2,3]`
    - The _sum_ of previous jumps, e.g. jumped 8. You could have jumped 5 through jumping either (5) or (1,1,3) or (4,1) or (1,4) or (2,1,1,1) etc. But all you need to pass is the sum of your jumps as `8`.
    - Other sophisticated problems may require you to pass more complex variables down your path.
## Under what conditions do I stop tree traversal? What do I return (or do — in case of a Void function)?

### Jump StairCase
Under what condition(s) does your tree not grow / hit a leaf / end a path / terminate progression return something that's not recursive itself. Example: 
- End if reached the top of the stairs. Or end if you jumped passed the targeted stair. Example:

```swift
if totalJumpSum == targetSum { return 1 }
```

Note: If you were dealing with another variant of the climbing stair question and needed to return the actual jumps and not just the total 'numbers'  then: 
```swift
if totalJumpSum == targetSum { answerArray += [currentJumps + lastJump] } // answerArray += [[jump2,jump5] + [jump3]
```

Don't forget you need to stop also when you go beyond the desired target. Example:

```swift
if totalJumpSum > targetSum { return 0 }
```
So
- Return 1 if you found an answer, that would increase the total count.
- Return 0 if needed to terminate, but didn't find a good answer.

As the coder you have to identify when your tree should stop growing / reaches its _desired_ target. You also have to be considerate of traversing in parts of the tree/path where you know for sure that your tree will _not_ reach its desired target. 

### Move in 2D Graph ???? Add link to question
- If you were solving a grid problem which is more or less the same problem like the climbing stairs, but only in 2D, then you'd end if: you reached the other end of a 2D grid. Or end if you went outside the grid. 
Try to come up with logic for this.

## So I didn't hit a base case. What then? 

- Call your recursive function again. 
- Pass whatever **path specific** information was needed. 
    - You always need to apply the mutation given to your state. 
    - Then pass new mutation to your helper function.
- **Note:** You should not do any other checks at this point. Even if you know jumping 2 steps from 9, will go beyond 10, you shouldn't add logic to skip calling the recursive function. You should just call your recursive function. Let it terminate / exit early in the **next run** of your function's base case checks. 
This was one of those of confusing points in recursion. I was never sure if I needed to be smart and skip calling my recursive function again. But now I know I shouldn't be smart. 
Basically don't mix-match the recursive calling with an exit early.


Your overall structure should be like this: 

```swift

func findSolution(inputs: [Inputs]) {
    /* 
    Call helper with its current state.
    Don't try to alter the state. 
    */


}

func helper(pathState: State) -> Value {
    /*
    if reached_base_case:
        return base_case_value    
    else reached_another_base_case:
        return other_base_case_value 
    else traverse_down_tree: 
        - Traverse down the tree. 
        - Update the path/stack.
        - Return the result of both left and right children together. Each question has a different trick for combining. Example of different ways to combine:
            - With `+`
            - With `&&`
            - With `||`
            - With `==`
            - other ways
        This will ultimately and always lead to hitting a base case i.e. stop recursing. Trust the process. 😉
        
        In the case of the staircase question: we combine the result of left & right simply just by adding their sum. 
    */
}

```
### Why does my code continue infinitely?
It implies that either you:
- Haven't handled all your base cases.
- Didn't change the state upon traversing. 

## Figure out how to call your recursive function from your main function
For counting the number of ways, you're starting from stair `0`. And your starting/current answer is `0`. Your desired stair is also another parameter that you need to pass...


### Summary of steps
So to do each of the three steps we discussed earlier: 
1. [What information do I need to pass down for each path](): The sum of the jumps so far.
2. [Under what conditions do I stop tree traversal?](): If I reach the targeted stair. Or if jumped passed it.
3. [So I didn't hit a base case. What then?](): Recursively call the function. Combine the results of each node using `+`. Don't be smart: Don't try not calling your function. Let it exit any of its base cases in the next function execution.
4. [Figure how to make the first recursive call](): Pass the right values as discussed. 

## Code 1 - Simplest choice: 

```swift
func howManyWays(num: Int) -> Int {
    return helper(origin: 0, target: num, ans: 0)
}

/// Recursively returns the total number of steps
/// - Parameters:
///   - origin: arrived step/node
///   - target: desired step/node
///   - ans: current total number of ways
/// - Returns: Total number of ways from a given step/node
func helper(origin: Int, target: Int, ans: Int) -> Int {
    if origin == target {
        return 1
    } else if origin > target {
        return 0
    } else {
        return ans + helper(origin: origin + 1, target: target, ans: ans) + helper(origin: origin + 2, target: target, ans: ans)
    }
}

print(howManyWays(num: 4))
```

## Code 2 - Pass down a computed property
So instead of passing down both target and current, we can pass `remainingSteps`

```swift
func howManyWays(num: Int) -> Int {
    return helper(remainingSteps: num - 0, ans: 0)
}

/// Recursively returns the total number of steps
/// - Parameters:
///   - origin: arrived step/node
///   - target: desired step/node
///   - ans: current total number of ways
/// - Returns: Total number of ways from a given step/node
func helper(remainingSteps: Int, ans: Int) -> Int {
    if remainingSteps == 0 {
        return 1
    } else if remainingSteps < 0 {
        return 0
    } else {
        return ans + helper(remainingSteps: remainingSteps - 1, ans: ans) + helper(remainingSteps: remainingSteps - 2, ans: ans)
    }
}

print(howManyWays(num: 4))

```

# Post 2. DO NOT POST IN SAME POST. LINK TO IT!!!

If you haven't read the previous post on [How to Think Recursively](http://mfaani.com/posts/interviewing/how-to-think-recursively-part1/), then first read that. 

This post re-applies the steps mentioned in the previous post on a new question.

## Question
How many ways can we generate well-formed parenthesis?

Examples:
- if `n = 1` then we can only form `()`
- if `n = 2` then we can form `(())` and `()()`
- if `n = 3` then we can form `((()))`, `(())()`, `()(())`, `(()())`, `(), (), ()`


Let's try applying our 4 steps: 
### Summary of steps
So to do each of the three steps we discussed earlier: 
1. [What information do I need to pass down for each path](): The total number of opens, the total number of closed or perhaps how many more have we opened vs closed. 
2. [Under what conditions do I stop tree traversal?](): If I've opened more parenthesis than our target. If I've closed more than we've opened. If I've closed more than our target. 
3. [So I didn't hit a base case. What then?](): Recursively call the function. Bifurcate into opening and closing both. Don't be smart: all code-paths at this point should call your function again. Let it exit any of its base cases in the next function execution.
4. [Figure how to make the first recursive call](): Well we start from `0` open/close parenthesis.

```swift { hl_lines=["17"]}
enum P: String {
    case open = "("
    case close = ")"
}
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

In the highlighted lines, is only executed if `openedCount < num`. But it's cleaner if we just allow it to be called and handle it in the base case exits. 

## Cleaner solution
In our `else`, we're just calling the recursive function. We don't have any conditions. We haven't sneaked in any base-case handling into there. 
All our base cases are moved to the beginning of the function. 

This has two advantages: 
- Groups all the bases cases together. This makes it a lot easier to process logic.
- Removes indentation from our code. 

```swift { hl_lines=["14-17"]}
enum P: String {
    case open = "("
    case close = ")"
}
func generateParen(num: Int) -> [String] {
    var ans: [String] = []
    
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

In the above, we have three base cases. Followed by recursive calls. It's cleaner. We clearly isolate base case exits from recursive function calls. Because we added this cleanliness, we can identify something that can be improved. 

The highlighted lines above overlap, we can combine them into `if openedCount > num`. Just try out the numbers in an example and you'll see. 

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