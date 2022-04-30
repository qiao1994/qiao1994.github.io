# LeetCode : reverse-linked-list

```
package main
import "fmt"

type ListNode struct {
    Val int
    Next *ListNode
}
func main() {
    node5 := ListNode {5, nil}
    node4 := ListNode {4, &node5}
    node3 := ListNode {3, &node4}
    node2 := ListNode {2, &node3}
    node1 := ListNode {1, &node2}
    head := ListNode {0, &node1}
    reverseList(&head)
}

func show(head *ListNode) {
    current := head
    for current != nil {
        fmt.Println("值:", current.Val);
        current = current.Next
    }
}
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func reverseList(head *ListNode) *ListNode {
    if (head == nil) {
        return nil
    }
    var prev *ListNode
    prev = nil
    current := head
    next := head.Next
    for current != nil {
        if prev != nil {
            fmt.Println("prev:", prev.Val);
        } else {            
            fmt.Println("prev:nil");
        }
        if current != nil {
            fmt.Println("current:", current.Val);
        } else {            
            fmt.Println("current:nil");
        }
        if next != nil {
            fmt.Println("next:", next.Val);
        } else {            
            fmt.Println("next:nil");
        }
        fmt.Println("=========");
        current.Next = prev //真正把current的指针指向前序节点的操作
        prev = current //维护prev
        current = next //维护current
        if (current != nil) {
            // 如果current已经为nil，则下一次不会再进入循环体；同时next.Next越界
            next = next.Next //维护next
        }
    }
    show(prev)
    return prev
}

```

