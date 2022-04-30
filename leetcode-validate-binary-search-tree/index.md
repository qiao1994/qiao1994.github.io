# LeetCode : validate-binary-search-tree

```
package main

type TreeNode struct {
    Val int
    Left *TreeNode
    Right *TreeNode
}

func isValidBST(root *TreeNode) bool {
    if root == nil {
        return true
    }
    return helper(root, nil, nil)
    
}

func helper(root *TreeNode, low *int, high *int) bool {
    if ((low != nil && root.Val <= *low) || (high != nil && root.Val >= *high)) {
        return false
    }
    if root.Left != nil {
        if !helper(root.Left, low, &root.Val) {
            return false
        }
    }
    if root.Right != nil {
        if !helper(root.Right, &root.Val, high) {
            return false
        }
    }
    return true
}

```

