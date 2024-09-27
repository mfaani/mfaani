---
title: "Asteroids Collision"
date: 2023-11-20T09:00:08-05:00
category: interviewing
tags: [swift, arrays, leetcode, stack, asteroid collision]
description: "Of the most fun leetcode challenges I've ever done"
cover:
    image: "asteroid-collision.webp"
    alt: "An AI generated image of asteroids"
    relative: false
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

Today we're solving https://leetcode.com/problems/asteroid-collision/

Imagine if we had the following:

```
1    5   3   8   6
->   ->  <-  <-  ->
```


Each number represents the size of an asteroid. Each asteroid is either going left or right. Bigger asteroids destroy smaller asteroids. Asteroids with same size both get destroyed. If two asteroids are going in the same direction, they don't hit each other, because all are going in the same speed.

The challenge is to return: which asteroids will remain at the end?


We can demonstrate the direction and size with an array where positive values mean moving towards _right_ while negative values mean moving towards _left_: `[1,5, -3, -8, 6]`

Let's start from the beginning:

```
start:  [1,5, -3, -8, 6]
[1]   + 5: Same direction. Don't collide. [1,5] remain.
[1,5] + -3: Different directions. -3 is destroyed. [1,5] remain.
[1,5] + -8: Different directions. 5 is destroyed. [1,-8] remain. 
[-8] continues towards 1 and collides and destroys 1. [-8] remains. 
[-8]  + -6: Same Direction. Don't collide. [-8, -6] remain.
end:    [-8, -6]
```

## Solution One

### Pseudocode

```md
copy array. 
loop through: left to right
    for each asteroid, compare it with its next asteroid:
        if left asteroid is going right and next asteroid is going left, then:
            figure out the collision:
            remove destroyed asteroids
            pass new indexes for comparison
        if there's no sign change, then:
            continue
```

### Code

```swift
class Solution1 {
    var i = 0 
    func asteroidCollision(_ asteroids: [Int]) -> [Int] {
        var asteroids = asteroids
        while i < asteroids.count {
            handleCollision(from: i, arr: &asteroids)
        }
        return asteroids
    }
    
    func handleCollision(from i1: Int, arr: inout [Int]) {
        let i2 = i1 + 1
        guard i1 >= 0 && i1 < arr.count && i2 >= 0 && i2 < arr.count else { 
            i += 1
            return            
        }
        if arr[i1] > 0 && arr[i2] < 0 {
            if arr[i1].magnitude > arr[i2].magnitude {
                // remove right asteroid
                arr.remove(at: i2)
                // redo comparison for same index, but with updated next index.
                handleCollision(from: i1, arr:&arr)
            } else if arr[i1].magnitude == arr[i2].magnitude {                
                // remove both asteroids
                arr.remove(at: i2)
                arr.remove(at: i1)
                // redo comparison for same index, but with updated next index.
                handleCollision(from: i1 - 1, arr: &arr)
                i -= 1
            } else {
                // remove left asteroid
                arr.remove(at: i1)
                handleCollision(from: i1 - 1, arr:&arr)
                i -= 1
            }
        } else {
            // move forward without removing any asteroids
            i += 1
        }
    }
}
```

### Time Complexity
- for loop: O(n)
    - remote(at: Int): O(n)
- Total: O(n * n)

### Space Complexity
`O(1)`: If the parameter was passed as `inout` instead, then it would have been `O(1)`, because we really didn't need another variable. We just did it to make it mutable. 

## Solution Two

### Idea
Let's see why using a stack can be more effective than an array in this context:


> A stack is a linear data structure following the **Last In, First Out (LIFO)** principle, where the most recently added item is the first to be removed, using operations like push, pop, and peek. In contrast, an array allows direct access to any element via indexing, which isn't essential for this problem.

even if technically built on an array. We don't need random access or frequent inserts/removals—key array traits.

💡 We need to (repeatedly) compare _adjacent_ objects at the _end_ of the array, a stack structure aligns perfectly, even if technically built on an array. We don't need random access or frequent inserts/removals—key array traits.  
💡 Stacks are usually perceived as 'vertical' data structures while arrays are perceived as 'horizontal' data structures. However you shouldn't limit them to such. Both can be used in horizontal or vertical contexts. 

### Pseudocode

```md
for each asteroid
   add it to stack
   handle collisions in stack recursively — until there's no collision
endloop

return stack
```

### Code

```swift
class Solution2 {
    var stack: [Int] = []
    func asteroidCollision(_ asteroids: [Int]) -> [Int] {
        
        for asteroid in asteroids {
            stack.append(asteroid)
            handleStackCollision()
        }        
        return stack
    }
    
    func handleStackCollision() {
        guard stack.count > 1 else { return }
        let right = stack[stack.endIndex - 1]
        let left = stack[stack.endIndex - 2]
        if left > 0, right < 0 {
            if left.magnitude == right.magnitude {
                // pop both
                stack.removeLast(2)
            } else if left.magnitude > right.magnitude {
                // pop right
                stack.popLast()
            } else {
                // pop left
                stack.remove(at: stack.count - 2)
            }
            handleStackCollision()
        }
    } 
}
```

### Time Complexity
- for loop: `O(n)`
    - append: `O(1)`
    - traverse back: `O(n)`
    - removeLast(2): `O(2)`
    - remove(at: stack.count - 2): `O(1)`
- Total: `O(n)`
- NOTE1: The maximum number of collisions is `O(n - 1)`. As a result we won't ever end up traversing the whole array each time. Because of that, the total is `O(n)`.
- NOTE2: `remove(at: Int)` time complexity depends on the index.
- NOTE3: With the leetcode test cases, solution two had only slightly better results. However when I tested things in Xcode with a much larger dataset, solution one errored out due to stack overflow. Solution two worked just fine. My point is Leetcode test samples can mess up your timings and give you the wrong impression about the timings of your solutions.

### Space Complexity
Stack creation: `O(n)`

### Interesting notes

I was initially trying to use the stack with just recursion. Then I tried to solve it with iteration. It didn't work. 
Simply put I had to iterate for each new addition. But then do recursion until asteroids stopped colliding. 

In that sense it was a new challenge, because I've always seen challenges where I either had to iterate with a simple for-loop or do recursion, never both, let alone have it combined with stacks. It was a really fun challenge. 

> Use **iteration** to get to all items. Then depending on your need, use **recursion** or whatever's necessary to process the current iteration.

## Conclusion and things I learned along the way

- Have a list of different data structures in front of you. It can help trigger your brain. I wasn't able to come up with the idea of using a Stack on my own, but some tips for being able to come up with it are: if you have some 'undoing' to do, like undo adding something, then a stack can be a good choice. FWIW this problem could have also been solved with a **linked list** as well. I just didn't code that. 
- Writing the pseudocode with indentation will make it easier to reason with, visualize the steps and come up with its time complexity a lot easier. 
- Time Complexity of `remove(at: Int)`, is **not** always `O(n)`. It's more accurate to say `O(array.count - i)`. Example for an array of `1000` elements:
  - `array.remove(at: 2)` would be more or less `O(999)` because you're shifting 999 items in the array.
  - `array.remove(at: 998)` would be more or less `O(1)` because only you're only shifting one item in the array.
- Leetcode test cases may not always be a perfect reflection of the time complexity of your solutions.