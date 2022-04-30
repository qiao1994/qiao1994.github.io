# 合并两个有序链表


## 递归

补：2019-07-04发现了屌炸天的递归算法，补上它

理解这个递归的核心是，该函数每次都会返回剩余元素中最小的一个；或者一个链表已空，则返回剩余的；这一点保证了head1.next = mergeRecursion(head1.next, head2)一定能链接到下一个最小的元素，这里依然利用了递归深入的过程中就能保存顺序这一特性，代码极其简单。

```go
func mergeRecursion(head1, head2 *node) *node {
    if head1 == nil {
        return head2
    }   
    if head2 == nil {
        return head1
    }   
    if head1.val < head2.val {
        head1.next = mergeRecursion(head1.next, head2)
        return head1
    } else {
        head2.next = mergeRecursion(head1, head2.next)
        return head2
    }   
}
```



## 遍历

遍历算法和合并有序数组思路基本一致

```go
package main
import "fmt"

type node struct {
    val int
    next *node
}

func main() {
    //0->2->4->6->8->10
    head1 := &node{10, nil}
    for i:=8; i>0; i=i-2 {
        head1 = &node{i, head1}
    }

    //1->3->5->7->9
    head2 := &node{9, nil}
    for i:=7; i>0; i=i-2 {
        head2 = &node{i, head2}
    }

    head := merge(head1, head2)

    for head != nil {
        fmt.Println(head.val)
        head = head.next
    }
}

//合并两个有序链表
func merge(head1, head2 *node) *node {
    if head1 == nil {
        return head2
    }
    if head2 == nil {
        return head1
    }
    head := &node{}
    node := head
    node1 := head1
    node2 := head2
    for (node1 != nil) || (node2 != nil) {
        if node1 == nil {
            node = appendNode(node, node2)
            node2 = node2.next
            continue
        }
        if node2 == nil {
            node = appendNode(node, node1)
            node1 = node1.next
            continue
        }
        if node1.val < node2.val {
            node = appendNode(node, node1)
            node1 = node1.next
            continue
        } else {
            node = appendNode(node, node2)
            node2 = node2.next
            continue
        }
    }
    return head
}

//辅助函数
func appendNode(node, node1 *node) *node {
    if node == nil {
        node = node1
    } else {
        node.next = node1
        node = node.next
    }
    return node
}
```


