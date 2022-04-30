# 求链表的中间节点


一下能想到的是暴力解法，先遍历链表获取链表节点总数，然后求出中点的索引值，这样的时间复杂度是O(n)；

稍微优化一下的话就是使用经典的快慢节点的方式，每次遍历时快节点前进两位，慢节点前进一位，当快节点走到链表结尾时，慢节点所在的位置就是链表中点，这样的时间复杂度是O(n/2)，量级没有变化，但是会快一些。


```go
package main
import "fmt"

type node struct {
    val int
    next *node
}

func main() {
    //0->1...10
    head := &node{10, nil}
    for i:=9; i>=0; i-- {
        head = &node{i, head}
    }
    midpoint := getMidpoint(head)
    fmt.Println(midpoint)
}

//获取链表中点
func getMidpoint(head *node) int {
    slow, fast := head, head
    slowIndex, fastIndex := 0, 0
    for (fast != nil) && (fast.next != nil) {
        slow = slow.next
        slowIndex++
        fast = fast.next.next
        fastIndex = fastIndex + 2
    }
    return slowIndex
}
```


