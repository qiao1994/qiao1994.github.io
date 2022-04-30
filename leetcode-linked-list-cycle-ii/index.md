# LeetCode : linked-list-cycle-ii

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
    node4.Next = &node3
    result := detectCycle(&head)
    fmt.Println("值:", result.Val);
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
func detectCycle(head *ListNode) *ListNode {
    if head == nil {
        return nil
    }
    fast := head
    slow := head
    hasCycle := false
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            hasCycle = true
            break
        }
    }

    if (hasCycle == false) {
        return nil
    }

    start := head
    for start != slow {
        start = start.Next
        slow = slow.Next
    }

    return start
}
```

