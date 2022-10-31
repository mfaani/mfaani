---
title: "How to Think Recursively"
date: 2022-10-30T17:19:37-04:00
draft: true
---

/*
How to think recursively: 
Just follow these steps: 

- Know what it means to travel in a DFS vs BFS. Draw diagrams for yourself. If you have mastered this, then don't dive deeper yet. 
    - Try what you've learned with the most simplest examples: fibonacci problem or 'How many different ways you can climb a staircase' problems are great problems. In this post, I'll use the 'How many different ways you can climb a staircase' problem throughout my steps. 

- Try learning about the different classic (but simple) problems that are solved using Graphs/trees. Just knowing the variant kinds of problems and how they work visually will help you write your own code easier. 
- Use a paper and try to visually solve the question in the simplest form of a tree example (a root and two leafs).
- Then make your tree bigger by adding one more level to your tree and try to visualize the solution again. If you struggle here, then don't go any further until you figure it out. 

## What information do I need to pass down to my path, so I can have all the variables needed to make a decision?
- A tree is basically made up of multiple paths. Each path needs to be able to maintain **its own state**. 
- What information needs to be passed down a path so you can know whether a path is an answer or not. Example of things you need to pass down are: 
    - The previous actions you took, e.g. Jumped 2, then jumped 3. Your helper function should take that as an array of previous jumps `[3,5]`
    - The sum of previous jumps, e.g. jumped 8. You could have jumped 5 through jumping either (5) or (1,1,3) or (4,1) or (1,4) or (2,1,1,1) etc. But all you need to pass is the sum of your jumps. 
    - How many right turns you've took so far (as opposed to left, up, down). You'd just pass 2, or 0 , or 6 or 23, etc. 

## Under what conditions do I stop tree traversal? 

Under what condition(s) does your tree not grow / hit a leaf / end a path / terminate progression return something that's not recursive itself. Example: 
- End if reached the top of the stairs. Or end if you jumped passed to targeted stair. 
- End if you reached the other end of a 2D grid. Or end if you went outside the grid. 
Try to come up with logic for this. Example:

```swift
if totalJumpSum == targetSum { answerCount += 1}
// or if you needed to return the actual jumps and not just the total 'numbers'  then: 
if totalJumpSum == targetSum { answerArray += currentJumps + lastJump } // answerArray = [jump2,jump5] + jump[3] = target10
```

Don't forget you need to stop also when you go beyond the desired target. Example:

```
if totalJumpSum > targetSum { // do nothing }
```

You might think what good does, 'do nothing' do for us? Well the answer is that, you won't be making any more recursive calls, because you've terminated, hit a base case, reached the end of you tree. 

What you have to understand is that not all leaves are paths that lead to answer...

## So I didn't hit a base case. What then? 

- You have to call your recursive function again. 
- You pass whatever **path specific** information was needed. 
    - Often you need to mutate 
- **Note:** You should not do any other checks at this point. Even if you know jumping 2 steps from 9, will go beyond 10, you shouldn't add logic to skip calling the recursive function. You should just call your recursive function. Let it terminate / exit early in your base case checks. 
  This is one of those of confusing points in recursion. I was never sure if I needed to be smart and skip calling my recursive function again. But now I know I shouldn't be smart. 
  Additionally you shouldn't exit early for what's something that's to be decided by the next traversal of tree. Just mutate, and call the recursive function. That's it. 


Your overall structure should be like this: 

```swift

func findSolution(inputs: [Inputs]) {
    // call helper with empty state and first change
}

func helper(pathState: State, change: Mutation) -> Value {
    let newState = getNewState(from: pathState, change)
    if newState.isTerminatingState {
        return terminatingValue
    } else if newState.isAnotherTerminatingState {
        return anotherTerminatingValue
    } else {
        return helper(pathState: newState, change: newState.newChange)
    }
}
```







*/

enum P: String {
case open = "("
case close = ")"
}
func countParan(num: Int) -> [String] {
var ans: [String] = []
func h(diff: Int, paren: P, toc: Int, currentPath: String) {
    //        print("diff:", diff, "currentPath:", currentPath, "total opened:", toc)
    if diff == 0 && toc == num {
        //            print("current:", currentPath, "newPart:", paren.rawValue)
        ans.append(currentPath)
    } else if diff < 0 {
        //            return 0
    } else if diff > num {
        // we've opened parenthesis more than we could have
        //            return 0
    } else {
        var total = 0
        if toc < num {
            h(diff: diff + 1, paren: .open, toc: toc + 1, currentPath: currentPath + P.open.rawValue)
        }
        h(diff: diff - 1, paren: .close, toc: toc, currentPath: currentPath + P.close.rawValue)
    }
}
//    return h(diff: 1, paren: .open) +  h(diff: -1, paren: .close)
h(diff: 1, paren: .open, toc: 1, currentPath: "(")
return ans

// start with diff = num
// if diff <0 exit
// if diff == 0 ~terminate~ it actually implies start
// if diff > 0 continue
// if diff == n  

}
print(countParan(num: 1))
print(countParan(num: 2))
print(countParan(num: 3))
