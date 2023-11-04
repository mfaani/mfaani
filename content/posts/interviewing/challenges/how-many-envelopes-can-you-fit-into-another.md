---
title: "How Many Envelopes Can You Fit Into Another?"
date: 2022-08-03T09:44:49-04:00
tags: [leetcode, longest increasing subsequence]
description: "something"
draft: true
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

### Question: 
How many envelops can you fit into another? 
Each envelope has a 2D representation. [4,5] -> width = 4, length = 5
How many envelops can you fit into one another without rotating any envelopes. Bare in mind you can't fit in two evelopes with same width or height.

This is question is very much like the Russian Doll question. Which that question itself is very similar to the *Longest increasing subsequence*

The high level answer is: 

1. Sort it by width
2. Then sort evelopes with same width by height. Sorting by height is the tricky part.

> The direction you sort is important. It can inverse the impact on your flow. I often got confused with the direction of sorting. So I wrote [Which Way Am I Sorting?](https://mfaani.com/posts/interviewing/arrays/which-way-am-i-sorting/)). In short the the array’s order will match with the *direction* of the angle bracket (`<` or `>`)  you use. Think of right and left, instead of up and down, or of ascending and descending.

So say you're given the following envelopes:

```
[ [5,4], [6,5], [6,9], [2,3], [6,8] ]
```

Depending on the order in the original array, sorting it by width will can result in any of the following arrays. 

```
[ [2,3], [5,4], [6,7], [6,9], [6,8] ]
or
[ [2,3], [5,4], [6,8], [6,9], [6,7] ]
or 
[ [2,3], [5,4], [6,9], [6,7], [6,8] ]
or
[ [2,3], [5,4], [6,7], [6,8], [6,9] ]
or
[ [2,3], [5,4], [6,9], [6,8], [6,7] ]
or
[ [2,3], [5,4], [6,8], [6,7], [6,9] ]

```

So now things are sorted by the width. Great. let's 

Let's just assume we ended up with this order:
```
[2,3],  [5,4],  [6,7],  [6,9],  [6,8]
 > nil✔️   4>3✔️   7>4✔️     9>7✔️     8>9❌
```

Based on this approach, four evelopes will fit. But we know that's incorrect. Because the last three items have an identical width of 6.

## How can we find envelopes that fit if we have multiple with same width?

If width is identical then just ignore it. Let's try it: 

```
[2,3], [5,4], [6,7],         [6,9], [6,8]
 > nil✔️  4>3✔️  5>4✔️      6==6 (width same)❌
```

This works. However it's not forward proof. Like if our initial width sort ended with [6,9] being the first item and also we had a **new** item at the end `[7,8]`

```
[2,3], [5,4], [6,9],          [6,7], [6,8]       [7,8]
 > nil✔️  4>3✔️  5>4✔️        6==6 (width same)❌    8>9❌
```

You can't add `7,8` any longer because `6,9` was selected before and `9` is bigger than `8`

To say things differently and narrow the problem down. Our problem is more or less like the following:

```
[5,4], [6,z],[6,x],[6,y], [7,8]
```
assumption: z > y > x 
You have to pick either x,y,z in a way that your pick is bigger than 4 and smaller than 8. 

If you sorted items with same height in ascending order then you wouldn't know which one to pick. You have to try and error — when width is `6`.

However if you sorted (items by same height) in descending order, and used an LIS approach then it be just an algorithmic approach because with LIS you know that:
- you may pick one only from x,y,z since it's already sorted in reverse. If it was sorted in ascending order then all of them would have been picked.
- You may end up not picking any if none of x,y,z satisfy the requirement of being 'bigger than 4 & smaller than 8'. 

So if we sorted the ones with same width in descending like below and did and LIS on all the heights then

```
[5,4],[6,9],[6,8],[6,7],[7,8]
```

Our selected envelopes will be: 

```
[5,4],[6,7],[7,8]
```