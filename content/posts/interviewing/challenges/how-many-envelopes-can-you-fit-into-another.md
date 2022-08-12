---
title: "How Many Envelopes Can You Fit Into Another?"
date: 2022-08-03T09:44:49-04:00
tags: ["array", "string-compression", "swift"]
description: ""
draft: true
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---


### Question: 
How many envelops can you fit into another? 
Each envelope has a 2D representation. [4,5] -> width = 4, length = 5
How many envelops can you fit into one another without rotating any envelopes. 

This is question is very much like the Russian Doll question. Which that question itself is very similar to the *Longest increasing subsequence*


*: subsequence: same order, but some elements are missing. This is in contrast to substring which doesn’t have any missing elements...

The high level answer is: 

Sort it by width
Then sort it by height. 
- Be careful as to to the direction you sort. It can inverse the impact on your flow. (You might want to see [Which Way Am I Sorting?](https://mfaani.com/posts/interviewing/arrays/which-way-am-i-sorting/))
- When sorting by height, you need to be considerate of the width as well, because two envelops with the same width can’t fit...
Also if you get confused by how the ` < ` works, think of it this way, the the array’s order will match with the `<` or `>` order you use. Think of right vs left, rather than up vs down. Nor think of ascending vs descending...



