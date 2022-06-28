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


 
