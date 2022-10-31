---
title: "How to Think Recursively"
date: 2022-10-30T17:19:37-04:00
draft: true
---


This post is a bit about the gotchas that I faced. The logic in principle should apply to most recursive problems. 

## General steps
- Know what it means to travel in a DFS vs BFS. Draw diagrams for yourself. If you haven't mastered this, then don't dive deeper yet. 
    - Try what you've learned with the most simplest examples: 'How many different ways you can climb a staircase' is  a great problem. In this post, I'll use this problem 'How many different ways you can climb a staircase' problem throughout my steps. 
    - Understand the term 'branching factor'. That means for each node, how many different ways you can go. If all you can do is jump one or jump three, then your branching factor is `2`. If you were able to only jump six, then your branching factor is just `1`. 

- Try learning about the different classic (but simple) problems that are solved using Graphs/trees. Just knowing the variant kinds of problems and how they work visually will help you write your own code easier. Knowing the various applications of DFS will help you even if you can't write the code. 
- Use a paper and try to visually solve the question in the simplest form of a tree example (a root and two leafs).
- Then make your tree bigger by adding one more level to your tree and try to visualize the solution again. If you struggle here, then don't go any further until you figure it out. 

## Ask yourself 'what information do I need to pass down for each path, so I can have all the variables needed to make a decision'?
- A tree is basically made up of multiple paths. Each path needs to be able to maintain **its own state**. The state can't be shared amongst other paths. As that would override your decision.  
The state can't be a property of a class nor a local function of your function. It has to be a argument of the function that you pass down as you traverse the tree. 
- Here are examples of things you may need to pass down depending on the question: 
    - The _previous actions_ you took, e.g. Jumped 2, then jumped 3. Your helper function should take that as an array of previous jumps `[2,3]`
    - The _sum_ of previous jumps, e.g. jumped 8. You could have jumped 5 through jumping either (5) or (1,1,3) or (4,1) or (1,4) or (2,1,1,1) etc. But all you need to pass is the sum of your jumps is `8`.
    - Other sophisticated problems may require you to pass more complex variables down your path.
## Under what conditions do I stop tree traversal? 

Under what condition(s) does your tree not grow / hit a leaf / end a path / terminate progression return something that's not recursive itself. Example: 
- End if reached the top of the stairs. Or end if you jumped passed the targeted stair. 
- If you were solving a grid problem which is more or less the same problem like the climbing stairs, but only in 2D, then you'd end if: you reached the other end of a 2D grid. Or end if you went outside the grid. 
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

You might think what good does, 'do nothing' do for us? Well the answer is: the 'do nothing' avoids making any more recursive calls, because you've terminated, hit a base case, reached the end of you tree. 

What you have to understand is that not all leaves are paths that lead to targeted answers. 

## So I didn't hit a base case. What then? 

- Call your recursive function again. 
- Pass whatever **path specific** information was needed. 
    - You always need to apply the mutation given to your state. 
    - Then pass new mutation to your helper function.
- **Note:** You should not do any other checks at this point. Even if you know jumping 2 steps from 9, will go beyond 10, you shouldn't add logic to skip calling the recursive function. You should just call your recursive function. Let it terminate / exit early in the **next run** of your function's base case checks. 
  This was one of those of confusing points in recursion. I was never sure if I needed to be smart and skip calling my recursive function again. But now I know I shouldn't be smart. 
  Basically don't mix-match the recursive calling with an exit early.


Your overall structure should be like this: 

```swift {linenos=true linenostart=1}

func findSolution(inputs: [Inputs]) {
    // call helper with empty state and first change
}

func helper(pathState: State, change: Mutation) -> Value {
    /*
    let newState = getNewState(from: pathState, change)
    if you hit a base / leaf (any end of a path of your tree ) / terminate: 
        return base case value    
    else if you hit another base case:
        return the other base case value 
    else if no base case hit, then we'd want to traverse down the tree: 
        return the result of your node by just traversing your tree down further. This will ultimately and always lead to hitting a base case. It will return itself. Trust the process.
        Note usually, you need to return the result of both left and right children together. Often you combine their results
            - With `+`
            - With `&&`
            - With `||`
            - other ways
        In the case of the how many ways, we combine the result of left & right by just adding their sum. 
    */
}
```

The two tricky parts in the above are all about figuring out what we discussed earlier: 
- [Under what conditions do I stop tree traversal?]()
- [So I didn't hit a base case. What then?]()





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
