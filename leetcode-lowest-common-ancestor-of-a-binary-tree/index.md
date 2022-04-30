# LeetCode : lowest-common-ancestor-of-a-binary-tree

```
package main

type TreeNode struct {
    Val int
    Left *ListNode
    Right *ListNode
}

func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root == nil || root.Val == p.Val || root.Val == q.Val {
        return root
    }
    left := lowestCommonAncestor(root.Left, p,q *TreeNode)
    right := lowestCommonAncestor(root.Right, p,q *TreeNode)
    if (left != nil) && (right == nil) {
        return left
    }
    if (left == nil) && (right != nil) {
        return left
    }
    return root
}

```

