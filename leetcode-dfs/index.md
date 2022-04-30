# LeetCode : dfs

```
package main
import "fmt"

type TreeNode struct {
    Val int
    Left *TreeNode
    Right *TreeNode
}


func main() {
/*
    3
   / \
  9  20
    /  \
   15   7
*/
    node15 := &TreeNode{15, nil, nil}
    node7 := &TreeNode{7, nil, nil}
    node9 := &TreeNode{9, nil, nil}
    node20 := &TreeNode{20, node15, node7}
    root := &TreeNode{3, node9, node20}
    dfs(root)
}

func dfs(root *TreeNode) {
    fmt.Println(root.Val)
    if root.Left != nil {
        dfs(root.Left)
    }
    if root.Right != nil {
        dfs(root.Right)
    }
}

```

