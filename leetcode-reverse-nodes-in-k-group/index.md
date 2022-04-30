# Leetcode : reverse-nodes-in-k-group

# reverse-nodes-in-k-group
题目链接：https://LeetCode.com/problems/reverse-nodes-in-k-group/

# 代码
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
    result := reverseKGroup(&head, 3)
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
func reverseKGroup(head *ListNode, k int) *ListNode {
    if head == nil || head.Next == nil || k < 2 {
        return head
    }
    // 以0->1->2->3->4->5的第一次循环为栗子
    dummy := &ListNode{-1, head}
    prev := dummy
    tail := dummy
    
    // 创建dummy
    // -1->0->1->2->3->4->5
    // prev = -1
    // tail = -1
    // head = 0
    
    for true {
        i := k
        // 把tail放到对应的位置
        for i > 0 && tail != nil {
            tail = tail.Next
            i--
        }
        if (tail == nil) {
            break
        }
        // tail = 2
        fmt.Println("tail值:", tail.Val);
        // prev = -1
        if prev != nil {
            fmt.Println("prev值:", prev.Val);
        }

        // 这句是为下个循环打基础 本次循环完成后链表为 -1->2->1->0->3->4->5 下次的prev为0 tail为5  head为3(已不需要head，所以此处的head只是一个flag)
        head = prev.Next  // head = 0 (本次循环完成后 0即为新一轮循环的prev)
        fmt.Println("--------");
        for prev.Next != tail {
            temp := prev.Next // temp = 1 当前链表为：-1->0->1->2->3->4->5
            prev.Next = temp.Next //temp = 1 当前链表为：-1->1->2->3->4->5

            temp.Next = tail.Next //temp = 1 当前链表为：-1->1->2->3->4->5 / 0->3
            tail.Next = temp // temp = 1 当前链表为：-1->1->2->0->3->4->5 此时已完成0的位置转移
            // 循环完成head到tail间所有元素的转移即可
            if temp != nil {
                fmt.Println("temp值:", temp.Val);
            }
        }
        fmt.Println("========");
        prev = head
        tail = head
    }

    return dummy.Next;
}
```
