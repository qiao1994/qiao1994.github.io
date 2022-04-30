# LeetCode : implement-stack-using-queues

```
package main
import "fmt"

//实现一个队列

type queue struct {
    val []int
}

func (this *queue) push(val int) {
    this.val = append(this.val, val)
}

func (this *queue) pop() int {
    result := this.val[0]
    this.val = this.val[1:]
    return result
}

func (this *queue) peek() int {
    return this.val[0]
}

func (this *queue) size() int {
    return len(this.val)
}

func (this *queue) empty() bool {
    if (0 == len(this.val)) {
        return true
    }
    return false
}

//实现队列结束

func main() {
    stack := Constructor()
    stack.Push(1)
    stack.Push(2)
    fmt.Println(stack.Top())
    fmt.Println(stack.Top())
    fmt.Println(stack.Top())
    //fmt.Println(stack.Pop())
    //fmt.Println(stack.Empty())
}

type MyStack struct {
    queue1 queue
    queue2 queue
}


/** Initialize your data structure here. */
func Constructor() MyStack {
    var stack MyStack
    stack.queue1 = queue{[]int{}}
    stack.queue2 = queue{[]int{}}
    return stack
}


/** Push element x onto stack. */
func (this *MyStack) Push(x int)  {
    //直接压入queue1
    this.queue1.push(x)
}


/** Removes the element on top of the stack and returns that element. */
func (this *MyStack) Pop() int {
    //把queue1中除了最后一个压入queue2,pop出queue1中的最后一个
    for (this.queue1.size() > 1) {
        this.queue2.push(this.queue1.pop())
    }
    result := this.queue1.pop()
    //把queue2中的元素放回queue1
    for (!this.queue2.empty()) {
        this.queue1.push(this.queue2.pop())
    }
    return result
}


/** Get the top element. */
func (this *MyStack) Top() int {
    //把queue1中除了最后一个压入queue2,pop出queue1中的最后一个
    for (this.queue1.size() > 1) {
        this.queue2.push(this.queue1.pop())
    }
    result := this.queue1.peek()
    this.queue2.push(this.queue1.pop())
    //把queue2中的元素放回queue1
    for (!this.queue2.empty()) {
        this.queue1.push(this.queue2.pop())
    }
    return result
}


/** Returns whether the stack is empty. */
func (this *MyStack) Empty() bool {
    return this.queue1.empty()
}


/**
 * Your MyStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.Empty();
 */

```

