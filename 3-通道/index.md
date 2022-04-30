# 3.通道

> Don’t communicate by sharing memory; share memory by communicating. （不要通过共享内存来通信，而应该通过通信来共享内存。）

* 通道是Go语言中唯一一个可以满足并发安全的类型
* 通道的容量如果大于0，则为有缓冲区通道；否则为无缓冲区通道
* 基本特性
  * 发送和接收操作都是互斥的
  * 发送和接收操作都是原子的
  * 发送操作和接收操作都是会阻塞的
* **发送操作时进入通道的并不是在接收操作符右边的那个元素值，而是它的副本**
* panic的来源
  * 对关闭了的通道进行发送操作
  * 试图关闭一个已经关闭了的通道；so：尽量让发送方来关闭通道

```
ch1 := make(chan int, 3)
ch1 <- 2
ch1 <- 1
ch1 <- 3
elem1 := <-ch1
```

* 单向通道的应用场景

  * 约束函数行为，下面的例子说明了我们实现`SendInt`方法时，只能从通道里读内容

    ```
    type Notifier interface {
        SendInt(ch chan<- int)
    }
    ```

  * 在函数声明的结果列表中使用单向通道，下面的例子说明了，得到该通道的程序，只能从通道中接收元素值

    ```
    func getIntChan() <-chan int {
        num := 5
        ch := make(chan int, num)
        for i := 0; i < num; i++ {
            ch <- i
        }
        close(ch)
        return ch
    }
    ```

    > 我们在调用`SendInt`函数的时候，只需要把一个元素类型匹配的双向通道传给它就行了，没必要用发送通道，因为Go语言在这种情况下会自动地把双向通道转换为函数所需的单向通道。

