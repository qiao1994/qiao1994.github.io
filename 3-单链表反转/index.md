# 单链表反转


递归反转链表的基本思路是先用递归深入到最后一个节点，把最后一个节点传递返回，作为新的头节点；跳出递归的过程中把每个节点的后一个节点指向自身，并把自身的next指向空(为了把原头节点的next清空)。

如果不用递归，从前到后遍历每个节点时，需要关注很多节点，以及很多边界情况；递归的方法比较简洁的原因是递归的深入过程中自然记录了节点顺序，就能够从后向前调整，每次调整都只关注两个节点即可。

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

    head = reverse(head);

    for head != nil {
        fmt.Println(head.val)
        head = head.next
    }
}

//递归反转链表
func reverse(head *node) *node {
    if head == nil || head.next == nil {
        return head;
    }
    nHead := reverse(head.next);
    head.next.next = head;
    head.next = nil;
    return nHead;
}
```


