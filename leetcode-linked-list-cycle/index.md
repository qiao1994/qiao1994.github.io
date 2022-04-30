# LeetCode : linked-list-cycle

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
    node3.Next = &node2
    result := hasCycle(&head)
    fmt.Println("值:", result);
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
func hasCycle(head *ListNode) bool {
    if head == nil {
        return false
    }
    fast := head
    slow := head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            return true
        }
    }
    return false
}

```

