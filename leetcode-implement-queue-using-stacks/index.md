# LeetCode : implement-queue-using-stacks

```
package main
import "fmt"

//实现一个栈

type stack struct {
    val []int
}

func (this *stack) push(val int) {
    this.val = append([]int{val}, this.val...)
}

func (this *stack) pop() int {
    result := this.val[0]
    this.val = this.val[1:]
    return result
}

func (this *stack) peek() int {
    return this.val[0]
}

func (this *stack) size() int {
    return len(this.val)
}

func (this *stack) empty() bool {
    if (0 == len(this.val)) {
        return true
    }
    return false
}

//实现栈结束

func main() {
    queue := Constructor()
    queue.Push(1)
    queue.Push(2)
    queue.Push(3)
    fmt.Println(queue.Pop())
    fmt.Println(queue.Pop())
    fmt.Println(queue.Pop())
}


type MyQueue struct {
    stack1 stack
    stack2 stack
}


/** Initialize your data structure here. */
func Constructor() MyQueue {
    var queue MyQueue
    queue.stack1 = stack{[]int{}}
    queue.stack2 = stack{[]int{}}
    return queue
}


/** Push element x to the back of queue. */
func (this *MyQueue) Push(x int)  {
    this.stack1.push(x)
}


/** Removes the element from in front of queue and returns that element. */
func (this *MyQueue) Pop() int {
    if (!this.stack2.empty()) {
        //stack2不为空,直接取stack2中的元素
        return this.stack2.pop()
    } else {
        if (!this.stack1.empty()) {
            //stack2为空,把stack1中所有元素pop到stack2中
            for (!this.stack1.empty()) {
                this.stack2.push(this.stack1.pop())
            }
        }
        //从stack2中pop元素
        return this.stack2.pop()
    }
}


/** Get the front element. */
func (this *MyQueue) Peek() int {
    if (!this.stack2.empty()) {
        //stack2不为空,直接取stack2中的元素
        return this.stack2.peek()
    } else {
        if (!this.stack1.empty()) {
            //stack2为空,把stack1中所有元素pop到stack2中
            for (!this.stack1.empty()) {
                this.stack2.push(this.stack1.pop())
            }
        }
        //从stack2中pop元素
        return this.stack2.peek()
    }
}


/** Returns whether the queue is empty. */
func (this *MyQueue) Empty() bool {
    return this.stack1.empty() && this.stack2.empty()
}


/**
 * Your MyQueue object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Peek();
 * param_4 := obj.Empty();
 */

```

