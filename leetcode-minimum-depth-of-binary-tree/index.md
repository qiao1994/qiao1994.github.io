# LeetCode : minimum-depth-of-binary-tree

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
    fmt.Println(minDepth(root))
}

func minDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    return dfs(root, 0)
}

func dfs(root *TreeNode, level int) int {
    level++
    leftLevel := 0
    rightLevel := 0
    if root.Left != nil {
        leftLevel = dfs(root.Left, level)
    }
    if root.Right != nil {
        rightLevel = dfs(root.Right, level)
    }
    if (leftLevel == 0) && (rightLevel == 0) {
        return level
    } else if (leftLevel != 0) && (rightLevel == 0) {
        //叶子节点
        return leftLevel
    } else if (leftLevel == 0) && (rightLevel != 0) {
        return rightLevel
    } else if (leftLevel < rightLevel) {
        return leftLevel
    } else {
        return rightLevel
    }
}

```

