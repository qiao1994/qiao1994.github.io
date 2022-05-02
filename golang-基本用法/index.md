# Golang基本用法

本文为2019春节期间用golang刷LeetCode时遇到的基础使用方式，备注一下，后续可直接复制取用。
## array
```
package main
import "fmt"

func main() {
    //dealare way1
    var arr1 [5]int //可以认为[5]int是一种类型
    fmt.Println(arr1)

    //dealare way2
    arr2 :=  [...]int{1,2,3,4,5}
    fmt.Println(arr2)

    //dealare way3
    arr3 :=  new([5]int) //得到指向数组的指针
    fmt.Println(arr3)

    //traversing way1
    for j := 0; j < len(arr2); j++ {
        fmt.Println(arr2[j])
    }

    //traversing way2
    for i,_ := range arr3 {
        fmt.Println(arr3[i])
    }
}
```

## heap
```
package main
import "fmt"
import "container/heap"
type IntHeap []int
func (h IntHeap) Len() int            { return len(h) }
func (h IntHeap) Less(i, j int) bool  { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *IntHeap) Push(x interface{}) {
    *h = append(*h, x.(int))
}

func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[0 : n-1]
    return x
}

func main() {
    h := &IntHeap{2, 1, 5}
    heap.Init(h)
    heap.Push(h, 0)
    heap.Push(h, 6)
    fmt.Println(h)
    fmt.Println(h)
    fmt.Println(h)
    heap.Push(h, 1)
    fmt.Println(h)
    fmt.Println(h)
}
```

## make
```
package main
import "fmt"

func main() {
    //array 两种声明方式一样,都会开辟内存空间
    var a1 [5]int
    a2 := [5]int{}
    a1[0] = 1
    a2[0] = 1
    fmt.Println(a1)
    fmt.Println(a2)
    //slice var不会开辟内存空间;:=如果传入值就会开辟，否则不开辟;make会开辟指定大小的内存空间;只有make的能直接赋值
    var s1 []int
    s2 := []int{}
    s3 := make([]int, 5)
    fmt.Println(s1)
    fmt.Println(s2)
    fmt.Println(s3)
    //map 同slice;var 不能直接赋值;:=的可以直接赋值
    var m1 map[int]int
    m2 := map[int]int{}
    m2[1]=3
    m3 := make(map[int]int, 5)
    m3[1]=2
    fmt.Println(m1)
    fmt.Println(m2)
    fmt.Println(m3)
}
```

## map

```
package main
import "fmt"

func main() {
		//map 同slice;var 不能直接赋值;:=的可以直接赋值
    var m1 map[int]int
    m2 := map[int]int{}
    m2[1]=3
    m3 := make(map[int]int, 5)
    m3[1]=2
    fmt.Println(m1)
    fmt.Println(m2)
    fmt.Println(m3)
    //判断map中某个元素是否存在
    if _, ok := map[key]; ok {
    //存在
    }
}
```



## queue

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
    queue := queue{[]int{}} //{}是初始化一个结构体,[]int{}是传入的值
    queue.push(1)
    queue.push(2)
    queue.push(3)
    fmt.Println(queue.pop())
    fmt.Println(queue.pop())
    fmt.Println(queue.pop())
}
```

## slice
```
package main
import "fmt"

func main() {
    //relation of slice and array
    //slice is a reference of array
    //array := [5]int{1,2,3,4,5}
    //slice := array[:]
    //fmt.Println(array)
    //fmt.Println(slice)


    //declare way1
    var slice1 []int //len = 0 and cap = 0
    fmt.Println(slice1)


    //declare way2
    slice2 := []int {1,2,3,4,5} //len = 5 and cap = 5
    fmt.Println(slice2)

    //declare and create : make ; make will create slice and slice's relation array
    slice3 := make([]int, 10) //len = 10 and cap = 10
    slice4 := make([]int, 10, 20) //len = 10 and cap = 20
    fmt.Println(slice3)
    fmt.Println(slice4)

    //declare and create : new ;
    slice5 := new([100]int)[0:30] //"new" allocate memory of array and assignment array's slices to slice5
    fmt.Println(slice5)
        //deferent between new and make : "new" allocate memory for type T and return the address of *T ; "make" create a object of type T and return it

    //slice use
    fmt.Println(slice2[0:3])
    fmt.Println(slice2[3:])
    fmt.Println(slice2[:3])
}
```

## stack
```
package main
import "fmt"

//实现一个栈

type stack struct {
    val []interface{}
}

func (this *stack) push(val interface{}) {
    this.val = append([]interface{}{val}, this.val...)
}

func (this *stack) pop() interface{} {
    result := this.val[0]
    this.val = this.val[1:]
    return result
}

func (this *stack) peek() interface{} {
    return this.val[0]
}

func (this *stack) size() interface{} {
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
    var stack stack
    stack.push(1)
    stack.push(2)
    stack.push(3)
    fmt.Println(stack.pop())
    fmt.Println(stack.pop())
    fmt.Println(stack.pop())
}

```

## string
```
package main
import "fmt"
import "strings"
import "strconv"

func main() {
    // string 是[]byte的别名
    // byte是uint8的别名,用来表示ascii;一个英文字符对应一个byte,一个中文字符对应三个byte
    // rune是int32的别名,用来表示utf-8[一个中文占三个字节]/unicode[一个中文占两个字节];一个rune对应一个UTF-8字符
    str := "中文test string"
    fmt.Println("str[0]:", str[0])
    // = string转[]byte,其实不转也是
    strByte := []byte(str)
    fmt.Println("strByte[0]:", strByte[0])
    // = string转[]rune
    strRune := []rune(str)
    fmt.Println("strRune[0]:", strRune[0])
    // = string求长度
    fmt.Println("strings.Count()-1:", strings.Count(str, "")-1)
    // = string切割
    fmt.Println("strings.Split", strings.Split(str, ""))
    // = string截取
    fmt.Println("str[:]", str[6:])
    // = string转int
    if strToNumber, err := strconv.Atoi("1"); err == nil {
        fmt.Println("strconv.Atoi()", strToNumber)
    }
    // = int转string
    fmt.Println("strconv.Itoa()", strconv.Itoa(1))
}
```

## struct
```
package main

import "fmt"

type man struct {
    name string
    age int
    weight int
    score []int
}

func main() {
    //创建结构体的方法1
    var qiao man //qiao的类型是man
    qiao.name = "qiao"
    qiao.age = 25
    qiao.weight = 140
    qiao.score = append(qiao.score, 1)
    qiao.score = append(qiao.score, 2)
    fmt.Println(qiao)
    //创建结构体的方法2
    yang := new(man) //yang的类型是指向man类型实例的指针
    yang.name = "yang"
    fmt.Println(yang)
    //创建结构体的方法3
    liu := man{}
    fmt.Println(liu)
    zhang := man{"zhang", 25, 140, []int{1,2,3,4,5}}
    fmt.Println(zhang)
    //创建结构体的方法4
    li := &man{}
    fmt.Println(liu)
    zhang := man{"zhang", 25, 140, []int{1,2,3,4,5}}
    fmt.Println(zhang)
    //创建结构体的方法4
    li := &man{}
    fmt.Println(li)
}
```

## 传值还是传指针

```
package main
import "fmt"

func main() {
    // int/float/bool/string/array 值类型
    // slice/map 指针类型
    i := 1; f := 1.1; b := true; s := "a"
    testBaseType(i, f, b , s)
    fmt.Println(i, f, b , s)
    arr := [...]int{1,1,1}; sli := []int{1,1,1}; m := map[int]int{1:1,2:2};
    testSeniorType(arr, sli, m)
    fmt.Println(arr, sli, m)
}

func testBaseType(i int, f float64, b bool, s string) {
    i = 2
    f = 2.2
    b = false
    s = "b"
}

func testSeniorType(arr [3]int, sli []int, m map[int]int) {
    arr[0] = 2
    sliCopy := make([]int, len(sli))
    copy(sliCopy, sli)
    sliCopy[0] = 2
    m[1] = 2
}
```

## sort

### 使用sort包内置的IntSlice结构

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	a := sort.IntSlice{2, 8, 3, 7, 9, 4, 1, 6}
	fmt.Println("排序前", a)
	// sort.Sort(a)
  a.Sort()
	fmt.Println("排序后", a)
}
```

sort.IntSlice是 []int 类型并绑定了四个方法，前三个是对排序的对象必须得拥有的（也就是说如果自己想对一个对象排序时，必须实现这些方法），但sort包已经给我们实现了。然后还绑定了一个 Sort() 方法

![img](https://img-blog.csdnimg.cn/8bcdfcfa985c45249a162f98a2c65e00.png) 

### 自定义结构体

自定义一个结构，实现Len()、Less()、Swap()、Sort()[可选]，即可使用sort函数，这样我们就可以为自己要排序的数据定义排序方式了。

```go
package main

import (
	"fmt"
	"sort"
)

type paixua []int

func (x paixua) Len() int {
	return len(x)
}
func (x paixua) Less(i, j int) bool {
	return x[i] < x[j]
}
func (x paixua) Swap(i, j int) {
	x[i], x[j] = x[j], x[i]
}
func (x paixua) Sort() {
	sort.Sort(x)
}

func main() {
	a := paixua{2, 8, 3, 7, 9, 4, 1, 6}
	fmt.Println("排序前", a)
	a.Sort()
	fmt.Println("排序后", a)
}
```

### 快捷方式

排序包还提供了，Ints、Float64s、Strings、Slice等方式供大家直接调用，如

```go
func main() {
	a := []int{5, 2, 7, 6, 4, 1, 3, 9}
	fmt.Println("排序前", a)
	sort.Ints(a)
	fmt.Println("排序后", a)
  // Slice (n2是一个结构体切片)
  sort.Slice(n2, func(i, j int) bool { return n2[i].x < n2[j].x })
}
```

