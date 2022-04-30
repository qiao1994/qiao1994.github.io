# LeetCode : binary-tree-level-order-traversal

```
package main
import "fmt"

type TreeNode struct {
    Val int
    Left *TreeNode
    Right *TreeNode
}


func main() {
    node15 := &TreeNode{15, nil, nil}
    node7 := &TreeNode{7, nil, nil}
    node9 := &TreeNode{9, nil, nil}
    node20 := &TreeNode{20, node15, node7}
    root := &TreeNode{3, node9, node20}
    fmt.Println(levelOrder(root))
}

func levelOrder(root *TreeNode) [][]int {
    if (root == nil) {
        return [][]int{}
    }
    result := [][]int{}
    queue := []*TreeNode{}
    queue = append(queue, root)
    for len(queue) != 0 {
        levelSize := len(queue)
        levelElements := []int{}
        for i:=0; i < levelSize; i++ { //每次都把当前队列取空,就是这一级的elements
            var item *TreeNode
            for index,value := range queue{
                item = value
                queue = queue[index+1:len(queue)]
                break
            }
            levelElements = append(levelElements, item.Val)
            if (item.Left != nil) {
                queue = append(queue, item.Left)
            }
            if (item.Right != nil) {
                queue = append(queue, item.Right)
            }
        }
        result = append(result, levelElements)
    }
    return result
}

```

