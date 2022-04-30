# 8.goroutine

# goroutine

goroutine是用户级线程，它的优势在于开销低、灵活(因为完全由用户自己控制，不需要等待系统调度)；劣势在于复杂，因为整个过程需要用户自己管理。但是go语言提供了一套强大的调度器，用于调度goroutine、对接系统级的线程。

# goroutine调度器

调度器包含三个主要元素G（goroutine的缩写）、P（processor的缩写）和M（machine的缩写）

* G就是指goroutine
* M指系统级线程
* P指可以承载若干个G，且能够使这些G适时地与M进行对接，并得到真正运行的中介
  * 收回G资源：当一个正在与某个M对接并运行着的G，需要因某个事件（比如等待I/O或锁的解除）而暂停运行的时候，调度器总会及时地发现，并把这个G与那个M分离开，以释放计算资源供那些等待运行的G使用。
  * 分配G资源：当一个G需要恢复运行的时候，调度器又会尽快地为它寻找空闲的计算资源（包括M）并安排运行。
  * 创建新的系统线程：M不够用时，调度器会帮我们向操作系统申请新的系统级线程，而当某个M已无用时，调度器又会负责把它及时地销毁掉

# 主goroutine和其他goroutine有何不同

主goroutine和其他goroutine是什么？

* 主goroutine执行的就是main函数对应的内容
* 其他goroutine是由主goroutine调用go语句显示的创建出来的

go函数的执行时间明显之后与go语句的执行时间，因为go语句做的事情就是把这个G放入待执行的队列，至于这个G什么时间被执行，以什么样的顺序执行不做保证。

所以主goroutine默认并不会保证其他goroutine是否能执行、是否按顺序执行，主goroutnie默认也不会等待其他goroutine结束再结束。

所以下面代码可能的执行结果有

* 按随机顺序输出0~n(n≤9) `部分子goroutine运行后，主goroutine才结束`
* 完全不输出 `当子goroutine还没来的及运行时，主goroutine就结束了，这种情况的可能性较大`

```go
func main() {
	for i := 0; i < 10; i++ {
		go func() {
			fmt.Println(i)
		}()
	}
}
```

# 怎样才能让主goroutine等待其他goroutine

* 让主goroutine sleep一段时间，这种方式的优势是简单粗暴；劣势是时间无法把握，所以一般不会采用这种方式。

* 通过通道进行主goroutine和子goroutine间的通信，当子goroutine都执行完成后，主goroutine再结束

  > 这种情况下通道只需要传输信号，可以使用空结构体值字面量 struct{}；这东西在go语言中只有一份，且所有程序都会用这一份变量。

```go

func main() {
	num := 10
	sign := make(chan struct{}, num)
	for i := 0; i < num; i++ {
		go func() {
			fmt.Println(i)
			sign <- struct{}{}
		}()
	}
	// 办法1。
	//time.Sleep(time.Millisecond * 500)
	// 办法2。
	for j := 0; j < num; j++ {
		<-sign
	}
}
```
