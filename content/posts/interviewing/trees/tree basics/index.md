---
title: "Tree Basics and some Swift helpers for leetcode"
date: 2023-09-11T11:16:01-04:00
category: [Interviewing]
tags: ['interviewing','trees', 'binary trees', 'binary search tree', 'tree traversal', 'in-order traversal', 'level-order traversal', 'swift',]
description: "Some gotchas with different kinds of trees, tree mappings and traversals"
---

I do leetcoding every once a while, but keep forgetting some tree basics. This post here it to help with that. 

## Types of Tree

Trees can have multiple children. Or on the special case of binary trees have only two. A binary tree is still different from a binary search tree (BST) where things follow a certain order. 

### Binary search tree
For a Binary tree to be a BST, it has to follow the following rules:
- left node must be smaller than parent.
- right node must be greater than parent.  

Basically `left < parent < right`

## Order of insertion matters for BST

### Example 1
Let's say you have the following numbers `[1,2,3,4]` in your tree. Depending on the order you add them into the binary search tree, the tree's visualization would be different. 

A: Inserting in the following order: `[4,3,2,1]` will create:

```
         4
        /   
       3 
      /
     2
    /
   1
```


B: Inserting in the following order: `[4,2,3,1]` or `[4,2,1,3]` will create:

```
         4
        /   
       2 
      / \
     1   3
```

C: Inserting in the following order: `[2,1,3,4]` or `[2,3,1,4]` will create:

```
       2 
      / \
     1   3
          \
           4
```

D: Inserting in the following order: `[3,4,1,2]` or `[3,1,4,2]` will create:

```
       3 
      / \
     1   4
      \
       2      
```


E: Inserting `[1,2,3,4]` will create 

```
         1
          \ 
           2
            \ 
             3
              \
               4
```
and so on. 

All the above are a BST made of 1,2,3,4. Because all conform to the two rules. Yet each is inserted with different order.


## Tree Traversal
- [Breadth First Search](https://en.wikipedia.org/wiki/Breadth-first_search) (BFS). Also known as level-order traversal. 
- [Depth First Search](https://en.wikipedia.org/wiki/Depth-first_search) (DFS)

### Binary Search Traversal

You can traverse the binary tree in the same fashion. However binary trees can be traversed differently which can come in handy with binary search trees: 
- **In Order:** left (recurse), self, right(recurse): To elaborate a bit further: If the current node has a left child, recursively visit this child.
Then, visit the node itself.
If the current node has a right child, recursively visit this child.

- **Pre Order:** self, left (recurse), right(recurse)

- **Post Order:**: left (recurse), right(recurse), self

All the different ways of traversing a BST are more or less a 'hinted DFS'. Meaning in a DFS, if you have multiple children, you just pick _whichever_ you like and just go deeper. However with in-order, pre-order, post-order, you pick a certain node first then continue down the tree again in a fashion similar to DFS. 

The term 'order' might put you off a bit, it's not top to bottom like a BFS, it's more about smallest number to biggest. Hence an in-order traversal will start from the bottom left where the smallest item is at and finish at the bottom right where the biggest item is at. 

That being said not every binary tree has an order or is a BST. So alternatively as a way to memorize you can think of nodes of just left and right as numbers grow left to right.

## Mapping of a Tree to an Array

Mainly two of types of the above traversal types are used for array to BST conversions: 
- Level-order traversal: More accurate, but not necessarily useful. Depends on your needs. 
- In-order traversal: More logical and useful. 
The most commonly used binary search tree traversal method is in-order: 

If we have an array of [1,2,3], we can turn it into a tree as such: 

### Example 2

```   
      2 
     / \
    1   3
```
In-order array: `[1,2,3]`  
level-order traversal: `[2,1,3]`

We can also see the same tree as:

```   
         2 
       /  \
      1     3
    /  \   /  \
  nil nil nil nil
```

In-order array: `[nil,1,nil,2,nil,3,nil]`  
level-order traversal: `[2,1,3,nil,nil,nil,nil]`

### Example 3:

```
        3
      /   \
     1      5
      \      \
       2      6
```

In-order array: `[1,2,3,5,6]`  
Level-order traversal: `[3,1,5,nil,2,nil,6]`  

or alternatively: 

```
          3
       /     \
     1          5
   /  \       /  \
nil    2    nil   6
     /  \        /  \
  nil  nil    nil  nil
```

In-order array: `[nil,1,nil,2,nil,3,nil,5,nil,6,nil]`  
Level-order traversal: `[3,1,5,nil,2,nil,6,nil,nil,nil,nil]`

### Question``
Go back to Example 1 above. Ask yourself how is the in-order & level-order traversal among all variations of `[1,2,3,4]` different from each other?

{{< details "Click to see answer 👇" >}}
  - in-order traversal for all will be: `[1,2,3,4]`
  - level-order would be different for each. 
{{< /details >}}

## Use Paper when debugging your mapping/traversal codes.

- Draw a sample case
- Then iterate through it. **Just like a debugger**. Think you're pausing and write down values for your properties. Then traverse and repeat. 
- Often times the order of traversal becomes more clear on paper and you realize your code isn't respecting the intended order. My algorithm was correct, yet I was getting a bad result because I the input array I created in my head was incorrect. As soon as I wrote down on paper, I realized where the conversion of the table to array was wrong. I corrected the array and validated that my algorithm works as expected...
- The more you do on paper, the better you get at visualizing things in your head. 

## Jargon
- **Traversals:**
  - BFS
  - DFS
  - BST
  - BST Traversal 
    - in-order
    - pre-order
    - post-order

- **Root:** The top node.
- **Leaf:** Any node that doesn't have a left or right node. 
- **Depth of a node:** The number of edges from the node to the root node.
- **Height of a tree:** The maximum number of edges from the root node to a leaf node.

- **Types of BST:**
  - Balanced tree: A tree in which the heights of the left and right subtrees of _any_ node differ by at most 1. In Example 1, the B,C trees are balanced.
  - Full binary tree: A binary tree in which every node has either _0_ or _2_ children.
  - A complete binary tree: A binary tree in which every level, except possibly the last level, is completely filled, and all nodes in the last level are as far left as possible.
  - A Degenerate tree: Every non-leaf node has just one child in a binary tree known as a Degenerate Binary tree. Example 1, the A,E trees are degenerate and considered not efficient for sorting.

  See [here](https://towardsdatascience.com/5-types-of-binary-tree-with-cool-illustrations-9b335c430254) for more.

### What's the benefit of a balanced Tree?
- A balanced tree will allow you to cut the tree into half by having half on its left and the other half on its right. Unlike a degenerate tree where 100% of the remaining nodes is still on the same side. Degenerate trees are bad for doing a binary search tree. They're more similar to a Linked List or an array. 

## Major Helper functions
- Tree Printer. See [here](https://gist.github.com/mfaani/e6b807442ce6768aab14fb03f1a77e11#file-binarytree-swift-L40-L60)
- Array to 'Balanced Height Binary Tree Search' Convertor
  - level-ordered array to tree. See [here](https://medium.com/nerd-for-tech/swift-leetcode-series-binary-tree-level-order-traversal-db7603af1bb3). Algorithm works only if  
  - in-order array to tree: See [here](https://gist.github.com/mfaani/e6b807442ce6768aab14fb03f1a77e11#file-binarytree-swift-L62-L79)
- Traversals. See [here](https://gist.github.com/mfaani/e6b807442ce6768aab14fb03f1a77e11#file-binarytree-swift-L23-L36)

## Summary
- There are different kinds of trees that shouldn't be confused with one another. When it comes to binary trees, you may question why the in-order of different trees ends up the same. It's due to the insertion order. 

- A balanced binary search tree is best for searching because you can cut in half. 

- Contrary to a level-order traversal has a 1:1 relationship with its tree visualization, an in-order traversal doesn't have a 1:1 relationship with its tree visualization. This is because of the different insertion orders. This means a level-order traversal is capable of reflecting the positions of all nodes. However an in-order traversal algorithm can't be reverse mapped to its original tree. So choose your algorithms wisely. 

Example: 

- You can easily map a `[1,nil,3]` using level-order mapping to: 

```
    1
  /   \
nil    3
```

- However `[1,nil,3]` using an in-order mapping can get mapped to a root node of `nil`, which will create a `nil` tree. 

I was confusing the two traversals and lost a tremendous amount of time, hence this blog post. 

## Acknowledgements
Shout out to [Josh Caswell](https://github.com/woolsweater) and [Tim Vermeulen](https://github.com/timvermeulen) for answering some of my questions.