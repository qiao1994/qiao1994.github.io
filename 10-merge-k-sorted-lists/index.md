# 合并多个链表


## 思路

* 把所有链表的头节点，建立一个最小堆，逐个从堆中拿下最小的节点，并用链表的后续节点补充
* 分治，分别两两合并

## 问题

* 分治和遍历的差别是：每次分治处理之后，样本数量都会降低1/2
* 分治的边界把握很重要

## 代码

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeKLists(lists []*ListNode) *ListNode {
    if len(lists) == 0 {
        return nil
    }
    begin, end := 0, len(lists) - 1
    for begin < end {
        mid := (begin + end - 1) / 2
        for i:=0; i<=mid; i++ {
            lists[i] = mergeTwoLists(lists[i], lists[end-i])
        }
        end = (begin + end) / 2
    }
    return lists[0]
}

//合并两个有序链表
func mergeTwoLists(head1, head2 *ListNode) *ListNode {
    if head1 == nil {
        return head2
    }
    if head2 == nil {
        return head1
    }
    if head1.Val < head2.Val {
        head1.Next = mergeTwoLists(head1.Next, head2)
        return head1
    } else {
        head2.Next = mergeTwoLists(head1, head2.Next)
        return head2
    }
}
```


