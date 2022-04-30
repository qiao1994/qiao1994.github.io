# LeetCode : swap-nodes-in-pairs

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
	result := swapPairs(&head)
	show(result)
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
func swapPairs(head *ListNode) *ListNode {
    // 边界条件
    if head == nil || head.Next == nil {
        return head
    }	

    // 初始化
    var prev *ListNode
    prev = nil
    current1 := head
    current2 := head.Next
    next := head.Next.Next

    for current1 != nil && current2 != nil {
	    if prev != nil {
		    fmt.Println("prev:", prev.Val);
	    } else {
	  	    fmt.Println("prev:nil");
	    }
	    if current1 != nil {
		    fmt.Println("current1:", current1.Val);
	    } else {
	  	    fmt.Println("current1:nil");
	    }
	    if current2 != nil {
		    fmt.Println("current2:", current2.Val);
	    } else {
	  	    fmt.Println("current2:nil");
	    }
	    if next != nil {
		    fmt.Println("next:", next.Val);
	    } else {
	  	    fmt.Println("next:nil");
	    }
    	fmt.Println("-----------");

    	// 调整指向
    	current1.Next = next
    	current2.Next = current1
    	if prev != nil {
	    	prev.Next = current2
    	} else {
    		head = current2
    	}
    	if current1.Next != nil {
		    fmt.Println("current1.next:", current1.Next.Val);
    	}
    	if current2.Next != nil {
	    	fmt.Println("current2.next:", current2.Next.Val);
		}
    	fmt.Println("============");
    	// 维护临时变量
    	prev = current1
    	current1 = next
    	if (next != nil) {
	    	current2 = next.Next
	    	if (next.Next != nil) {
		    	next = next.Next.Next
	    	} else {
	    		next = nil
	    	}
    	} else {
    		current2 = nil
    		next = nil
    	}
    }
    return head
}

```

