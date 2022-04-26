---
title: "Tips for solving array interview questions"
date: 2022-04-10T06:47:16-05:00
tags: ['Interviewing', 'tips', 'array', 'leetcode']
category: ['Interviewing']
draft: true
---

Every Array trick I've learned for solving Algorithm style questions: 

- Use hashmap when:
    - Need to count number of occurrences.
    - Question has something to do with pairs or you need to create your own mapping. 
    - Need to know the last occurrence of something.
    - etc.
- Exit early to simplify logic. Example when the array is empty or only contains a single item.
    - Don't exit early if the question itself explicitly says it has more than 1 item. 
- Create a local variable. So you can: 
    - Carry state
    - Carry last index (of something)
    - Count e.g. you need to count spaces, zeros or a certain character. Then solve the question.
- Two steps: loop once to inspect. Find a certain variable, count, pattern. Then loop again and solve the problem 
- Sort it. Then depending on the type of question:
    - Have an index and start from beginning
    - Have a start index and end Index and move them as needed. 
- Start from the end.
- Binary search i.e. use middle of array and so on.
- To preserver space, often you may need to just loop over certain indices as opposed to creating a new array. 
- Swift tip. To create a simple array of 1 to 10, do `Array(1...10)`. [1...10] will create `Array<ClosedRange<Int>>` instead.
- Math and index manipulation is always tough. First index, last index, empty array. `i - 1`, `i + 1`, are dangerous areas. 
- Make sure you return an answer from every case. e.g. 

```swift
func calc(str: String) -> Int
    for s in str {
        if s == "a" {
            return "a"
        } else {
            return "b"
        }
    }
    // MISSING RETURN here! 
}
```
And for what the recovering return is to be, you'd either be returning an empty base, or an untouched base. i.e. either return `""` or like `strings.first!` (in the case of longest common prefix)

- It's great to convert strings to an array of characters. But once you do that, then make sure you convert back the array of characters to String — when necessary.
- Knowledge of [`Range`](https://developer.apple.com/documentation/swift/range) vs. [`ClosedRange`](https://developer.apple.com/documentation/swift/closedrange) can be helpful.
    - `Range` can be empty. `0..<0` won't contain `0`. `isEmpty` is `true` in this case. 
    - `ClosedRange` can never be empty. `0...0` will contain `0`. `isEmpty` can never be `true`. Nor you can ever have a bound like `0...-1` as that's illegal. Because you can't form a range...

I also liked this: 


Also see: 
https://medium.com/algorithms-and-leetcode/want-to-crack-leetcode-problems-easily-dc825e27e423 and its links 

AND

array: kind of too general to say
linked list: almost never, besides as underlying structure for a queue
stack: when you need quick access to most recently accessed elements
DFS: most kinds of backtracking type problems, also just as a general search in graphs/trees.
BFS: level-order traversals in trees, finding the shortest path between A and B in a graph of uniform weight.
hash maps: too general to say, lots of uses. ie: counting occurrences of characters in a string, keeping track of index where a character last appeared, etc.
heap: priority queue for certain graph algorithms, when you need quick access to highest/lowest element already processed, but may need to access more than one element.
https://www.reddit.com/r/cscareerquestions/comments/97sz3d/comment/e4ar1ok/?utm_source=share&utm_medium=web2x&context=3