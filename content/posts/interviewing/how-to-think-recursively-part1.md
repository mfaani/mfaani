---
title: "How to Think Recursively - Part 1"
date: 2022-11-15T13:15:37-04:00
description: "How to break down a recursive question into smaller pieces"
category: "Interviewing"
tags: ["Recursion", "Dynamic Programming", "Algorithmic Thinking", "Climbing Stairs"]
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
    - The _sum_ of previous jumps, e.g. jumped 5. You could have jumped 5 through jumping either (5) or (1,1,3) or (4,1) or (1,4) or (2,1,1,1) etc. But all you need to pass is the sum of your jumps as `5`.
    - Other sophisticated problems may require you to pass more complex variables down your path.
## Under what conditions do I stop tree traversal? What do I return (or do — in case of a Void function)?

### Jump StairCase
Under what condition(s) does your tree not grow / hit a leaf / end a path / terminate progression return something that's not recursive itself. Example: 
- End if reached the top of the stairs. 

```swift
if totalJumpSum == targetSum { return 1 }
```

Note: If you were dealing with another variant of the climbing stair question and needed to return the actual jumps and not just the total 'numbers'  then: 
```swift
if totalJumpSum == targetSum { answerArray += [currentJumps + lastJump] } // answerArray += [[jump2,jump5] + [jump3]
```

- Don't forget you need to stop also when you go beyond the desired target. Example:

```swift
if totalJumpSum > targetSum { return 0 }
```
So
- Return 1 if you found an answer, that would increase the total count.
- Return 0 if needed to terminate, but didn't find a good answer.

As the coder you have to identify when you should stop traversing / recursing because either you:
- You reach the _desired_ target.  
- You reached an _undesired_ target.

If you have reached a desired or undesired target, then you should just traverse further down the tree.

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
            - If you have a local property and are updating your sum/max, then you don't need to return a value. You just mutate the property of yours...
            - Other ways
        
        This recursive call will ultimately always lead to hitting a base case i.e. stop recursing. Trust the process. 😉
    */
}

```

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

## Triage your recursion:

### Why does my code continue infinitely?
It implies that either you:
- Haven't handled all your base cases.
- Didn't change the state upon traversing.

### Should I return values at the end of my recursive functions? 
With most programming problems, you can solve it either. But often a choice becomes easier. 

### I can draw the tree and traverse it on paper. I can't do the code though. Any tips?
As humans we normally draw our trees layer by layer (BFS). However in code, we usually go deep first, then reach a leaf. Climb back then and try the next unvisited node. In code we typically do DFS not BFS. 

Understanding that difference helps. Now try this: 
- Draw the entire tree
- But as you want to find an answer, just go down one path i.e. go deep. 
- Ask yourself what causes mutation from previous node to next node in your path. 

### I'm passing two many variables. What can I do? 
If you're passing in a parameter that never changes, then that's often a variable that can be eliminated. 
Example we don't need to pass down the desired stair. Instead we can just pass the remaining stairs left to jump.

### Any last tips? 
Add documentation to your code. It often creates a rubber ducky moment for you...

## Out of scope
- Optimization / Memoization was intentionally left out of scope.


## More Reading

See [part 2](http://mfaani.com/posts/interviewing/how-to-think-recursively-part1/) of this article. 