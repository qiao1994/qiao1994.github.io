# LeetCode : bfs

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
    fmt.Println(bfs(root))
}

func bfs(root *TreeNode) []int{
    result := []int{}
    queue := []*TreeNode{}
    queue = append(queue, root)
    for len(queue) != 0 {
        var item *TreeNode
        for index,value := range queue{
            item = value
            queue = queue[index+1:len(queue)]
            break
        }
        result = append(result, item.Val)
        if (item.Left != nil) {
            queue = append(queue, item.Left)
        }
        if (item.Right != nil) {
            queue = append(queue, item.Right)
        }
    }
    return result
}

```

