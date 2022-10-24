---
title: "Trees"
date: 2022-06-26T18:00:55-04:00
draft: true
---

### Random tip: 

Allow your traversing to take in a function. Gives you more flexibility: 

```swift
func inOrder(_ root: TreeNode?, nodeHandler: (TreeNode?) -> ()) {
        if let left = root?.left {
            inOrder(left)
            nodeHandler(left)
        }
        
        nodeHandler(node)

        if let right = root?.right { 
            inOrder(right)
            nodeHandler(node)
        }
    }
```

#### Example usage: 
```swift
inOrder(root) {node in 
    // append nodes into an Array
}

inOrder(root) {node in 
    // print nodes
}

inOrder(root) {node in 
    // mutate certain nodes nodes
}
```

## Complexity

### Time Complexity
It's a factor of the height of the tree and the number of possible branches. 
branch ^ height

### Space
It's a factor of:
1. The size needed for the **stack** to grow in a tree until you can return. You only return when you hit a leaf of your tree, i.e. a base case of your recursive function. Most of the times your height is as big as your targetSum/bestSum/numberOfCharactersOfaTargetWord. Because you need smaller targetSum / smaller bestSum and smaller numberOfCharactersOfaTargetWord

2. The size of the item you need to carry. 

When you're trying to validate your tree complexity, it might be a good idea to try it with the worst i.e. smallest traversal ability. Example how to get to `10`, if you can only jump by `1`.
