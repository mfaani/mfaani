---
title: "Top Down vs Bottom Up"
date: 2024-10-24T21:55:58-04:00
draft: true
---


Tabulation (bottom up - starting from top left corner)
💡 Tabulation should just does its work without calling others. Every subproblem is solved and finished. You just ask for values of other sub problems when you need them.
💡 Memoization on the other hand, will need to call others. It relies on the `return`ing value of a smaller subproblem exactly at the time of calculating its own subproblem. Every sub problem calls the recursive function and is then returned the value of its sub problem — until you hit a base case. Then a values are bubbled back up and you can return a value.
💡 both approaches are repetitive, but one is iterative, while the other is recursive.
💡 for both approaches depend on the value of other sub problems. Only that one just *retrieves* them since they were computed *in advance*, while the other *computes* them *on-demand*.


Say you needed to calculate the number of 2s in an array: 

`[1,1]` => count = 0
`[2,1]` => count = 1
`[2,1,4]` => count = 1
`[,1,4,2]` => count = 1
and so on. 

To calculate this, you could do either:

- Start count from the beginning. Increment count as you find one. Return the final count at the end
- Start counting from the end, Increment the count as you find one. Return the final count at the end. 
- Return the total count at the last index. Let it recursively ask for previous values.


 





