---
title: "How Understanding State Machines Helps With Building Trees and Graphs"
date: 2022-10-06T08:54:48-04:00
category: "interviewing"
tags: ["trees", "dynamic-programming", "swift"]
description: "A different way of seeing tree explorations"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---
My team was dealing with a large flow, where user can transition from multiple states or sometimes skip certain states. We didn't have a centralized controller, every screen just had logic on where it should go next.  
This made it difficult for us to see all our logic at once. We asked around and was told state machines are a good fit for our situation.  
State machines are void of any UX. You just pass an event to it. Based on the event and the current state, you update to a new state and you get a command to act upon. 

A watered down example of a [State machine](https://gist.github.com/andymatuschak/d5f0a8730ad601bcccae97e8398e25b2) is: 

```
event           + current state         ->    command  
--------------------------------------------------------
.momEnteredHome + houseWasClean         -> .enjoyYourself  
.momEnteredHome + houseWasAMess         -> .cleanHouse  
.guestsArrived  + notAllGuestsArrived   -> .wait  
.guestsArrived  + allGuestsArrived      -> .serveDinner  
.guestsLeft     + houseIsClean          -> .cleanHouse
```  

Now let's see how that can be deemed similar to graphs...
 
## Interview Question
 
You are climbing stairs. It takes n steps to reach the top.
 
Each time you can either climb 1 or 2 steps. 

In how many distinct ways can you climb to the top?

Example if there are 3 steps to the top, then you can do:
```
- 0 --jump1--> 1 --jump1--> 2 --jump1--> 3 
- 0 --jump1--> 1 --jump2--> 3
- 0 --jump2--> 2 --jump1--> 3
```
Let's draw a graph for counting how many ways we can get to 3: 
!["tree"](/different-paths-to-top-of-stairs.jpg)

## How Graphing is similar to state machines: 
 
Not 100% similar, but to a certain point: when we apply the same concept:  

 
```apache
event  + currentState -> command
--------------------------------
jump 1 + from 0 -> arriveAtStep 1
    jump 1 + from 1 -> arrive at 2
        jump 1 + from 2 -> arrive at 3
        jump 2 + from 2 -> arrive at 4
    jump 2 + from 1 -> arriveAtStep 3
jump 2 + from 0 -> arriveAtStep 2
    jump 1 + from 2 -> arriveAtStep 3
    jump 2 + from 2 -> arriveAtStep 4
 ```

It's important to note that: 
- The only possible events are 'jump 1' and 'jump 2'. (If we were allowed to jump 3 steps, then 'jump 3' would have been another event as well)
- However the current state can be a different number each time.
- From each state we bifurcate apply **two** new events.
- Because we bifurcate, we have to _add_ the result (number of paths that lead to step `n` from node `m`) of the two branches. 
- A code-path terminates when it reaches `n` or above. 
   - If we reach `n` exactly then we return `1`. 
   - If go over `n`, then we return `0`.
- In state machines, once you reach the terminating event, then your state machine is finished. However since we've bifurcated _multiple_ times, the exploring will only terminate when **all** branches terminate.
 
### 💡Summary: 
- An Edge in a graph is like an event in a state machine.
- The _current_ node in a graph is like the states in a state machine.
- The _next_ node in a graph is like the after result of handling a command in a state machine.
- The end of a path is like reaching the end of a state machine. 

## Code

```swift
func howManyWays(num: Int) -> Int {
    return helper(origin: 0, current: 0, target: num)
}

func helper(origin: Int, current: Int, target: Int) -> Int { 
    if origin == target {
        return 1
    } else if origin > target {
        return 0
    } else {
        return current + helper(origin: origin + 1, current: current, target: target) + helper(origin: origin + 2, current: current, target: target)
    }
}

print(howManyWays(num: 4))
```

## Code using caching: 

```swift
var cache: [Int: Int] = [:]
func howManyWays(num: Int) -> Int {
    return helper(origin: 0, current: 0, target: num)
}

func helper(origin: Int, current: Int, target: Int) -> Int {
    if let val = cache[origin] {
        return val
    }
    var ans: Int
    defer {
        cache[origin] = ans
    }
    
    if origin == target {
        ans = 1
        return ans
    } else if origin > target {
        ans = 0
        return ans
    } else {
        ans = current + helper(origin: origin + 1, current: current, target: target) + helper(origin: origin + 2, current: current, target: target)
        return ans
    }
}
```