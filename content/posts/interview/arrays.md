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
    - Don't exit early if the question itself explicity says it has more than 1 item. 
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