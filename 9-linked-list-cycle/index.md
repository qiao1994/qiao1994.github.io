# 判断链表是否有环


## 思路

使用快慢两个指针，如果两个指针相遇，则链表有环；如果快指针先走到链表尾，则链表无环

## 问题

* 判等操作应该在指针迁移一次后再做，否则所有都会被判定为true
* 传入参数可能为空，异常处理需要作为一个下意识；这里把它放到对fast指针的判定一起判断

## 代码

```go
type ListNode struct {
    Val int
    Next *ListNode
}

func hasCycle(head *ListNode) bool {
    fast, slow := head, head
    for {
        if fast != nil && fast.Next != nil && fast.Next.Next != nil {
            fast = fast.Next.Next
        } else {
            return false
        }
        slow = slow.Next
        if fast == slow {
            return true
        }
    }
}
```


