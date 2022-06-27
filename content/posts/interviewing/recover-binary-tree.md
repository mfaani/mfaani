---
title: "Recover Binary Tree"
date: 2022-06-27T09:29:51-04:00
category: "interviewing"
tags: ["binary-tree", "foundations", "swift"]
description: "How do you fix a broken binary tree?"
editPost:
    URL: 'https://github.com/mfaani/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

## Question: 

You have a binary tree. But **only** two of its elements have been swapped. This makes it a faulty binary tree. 
The challenge is to swap those two elements. And fix the tree. 

## Solution
I knew I had to traverse it. But then what? 

With a little help from reading online, I realized I should traverse it, and store the values into an array. 
Then you just loop the array and find the bad indexes. 

Here's the tricky part:

Suppose your array is something like: 

```
[1, 2, 4, 3, 6, 7, 8]
```
You loop and find out 4 is in a bad spot. You swap it with 3.  And your tree is fixed. 


However if your array ends up like this: 

```
[1, 2, 8, 4, 6, 7, 3]
```

You loop and find out 8, is in a bad spot. You swap it with 4 and up with: 

```
[1, 2, 4, 8, 6, 7, 3]
```

but that's still incorrect. 

So once you have the array built, what you need to do is:

1. Find the first item that not in its place. Let's name this `f` (for first).
2. Find the last item that's not in its place. Let's name this `s` (for second).
3. Swap those two items. 


To show you step two visually: 

```
[1, 2, 8, 4, 6, 7, 3]
       ↑  ↑
       ❗️ ❗️ 
       f  s

[1, 2, 8, 4, 6, 7, 3]
       ↑  ↑     ↑  ↑    
       ❗️ ❗️   ❗️ ❗️ 
       f  ←⎯⎯⎯→  s       

Perform swap between `f` and `s`: 
[1, 2, 3, 4, 6, 7, 8]
```

The heart of all the code would be:

```swift
var fBadVal: Int? 
var sBadVal: Int? 
var didSwapFirst = false
for i in 0..<tree.count {
    if let next = tree[safe:i + 1], next.val < tree[i].val{
        if didSwapFirst == false { 
            fBadVal = tree[i].val
            didSwapFirst = true
        }
        
        sBadVal = tree[i + 1].val
    }
}
```

### Setup of bad Binary tree: 

```swift
class TreeNode {
    var val: Int
    var left: TreeNode?
    var right: TreeNode?
    init(_ val: Int, _ left: TreeNode?, _ right: TreeNode?) {
        self.val = val
        self.left = left
        self.right = right
    }
}

var one = TreeNode(1, nil, nil)
var eight = TreeNode(8, nil, nil) // bad position
var six = TreeNode(6, nil, nil)
var three = TreeNode(3, nil, nil) // bad position

var two = TreeNode(2,one, eight)
var seven = TreeNode(7, six, three)

// root node
var four = TreeNode(4, two, seven)

```

### Actual code: 

```swift
class TreeFixer {
    var tree: [TreeNode] = []
    
    func recoverTree(_ root: TreeNode?) {
        // traverse the tree 
        inOrder(root) { node in
            node.map {tree.append($0)}              
        }
        print(tree.map {$0.val})
        
        // find the elements where they're not matching...
        var fBadVal: Int? 
        var sBadVal: Int? 
        var didSwapFirst = false
        for i in 0..<tree.count {
            if let next = tree[safe:i + 1], next.val < tree[i].val{
                if didSwapFirst == false { 
                    fBadVal = tree[i].val
                    didSwapFirst = true
                }
                
                sBadVal = tree[i + 1].val
            }
        }
        print(fBadVal, sBadVal)
        
        // traverse again & swap elements
        inOrder(root) { node in 
            node.map { node in
                if node.val == fBadVal  {
                    node.val = sBadVal!
                } else if node.val == sBadVal { 
                    node.val = fBadVal!
                }
            }              
        }
        print("sorted tree: InOrderTraversal")
        inOrder(root) { node in
            print(node?.val)
        }
        
    }
    
    // Traverse tree and return a block handler for each node. 
    func inOrder(_ root: TreeNode?, nodeHandler: (TreeNode?) -> ()) {
        if let left = root?.left {
            inOrder(left) { node in
                nodeHandler(node)
            }
            
        }
        nodeHandler(root)
        
        if let right = root?.right { 
            inOrder(right) { node in 
                nodeHandler(node)
            }
        }
    }
}

extension Array {
    public subscript(safe index: Int) -> Element? {
        guard index >= 0, index < endIndex else {
            return nil
        }
        
        return self[index]
    }
}

let fixer = TreeFixer()
print(four.left?.val)
fixer.recoverTree(four)
```

## Time Complexity
- Traversing is `O(n)`.
- Appending to the array is `O(1)`.
- Traversing the array is `O(n)`.
- swapping in the tree is `O(n)`.

## Space Complexity
`O(n)` because you need an array. 
## Summary: 
- Often translating a tree into an array can be helpful.
- Using an `inOrder` method that takes a handler block can be helpful.
- Using a safe subscript for the array...something that Swift doesn't have can be helpful.
- **main trick** was to continuously look for 2nd index.