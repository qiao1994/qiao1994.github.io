# 动态数组


这里实现了一个能够动态扩容和缩容的数组，动态数组的核心在于扩容和缩容策略，这里采用的策略如下

* 扩容：当数组满了以后，把容量扩充为原来的两倍

* 缩容：当数组使用率低于1/4，把容量缩小为原来的一半

  > 不在低于1/2时就缩容是为了避免在临界点反复增删导致的频繁扩容缩容导致的性能问题

```go
package main
import "fmt"

//数据结构
type array struct {
    data []interface{}
    size int
    capacity int
}

//数组接口
type arrayInterface interface {
    add(int, interface{})
    remove(int) interface{}
    set(int, interface{})
    get(int) interface{}
}

//构造函数
func getArray(capacity int) *array {
    result := &array{}
    result.data = make([]interface{}, capacity)
    result.size = 0
    result.capacity = capacity
    return result
}

//扩容/缩容数组
func (this *array) resize(capacity int) {
    //创建一个指定大小的新数组
    newData := make([]interface{}, capacity)
    //把this.data中的数据搬到新数组中
    for i:=0; i<this.size; i++ {
        newData[i] = this.data[i]
    }
    //把this.data更新为新数组
    this.data = newData
    //更新容量
    this.capacity = capacity
}

//增
func (this *array) add(index int, element interface{}) {
    //合法性判断
    if (index < 0) || (index > this.capacity) {
        panic("index must >= 0 and <= capacity")
    }
    //判断是否需要扩容
    if len(this.data) == this.size {
        this.resize(this.size * 2)
    }
    //插入位置及其之后的位置,需要向后移动
    for i:=this.size-1; i>=index; i-- {
        this.data[i+1] = this.data[i]
    }
    //赋值
    this.data[index] = element
    //数组规模增加
    this.size++
}

//删
func (this *array) remove(index int) interface{} {
    //合法性判断
    if (index < 0) || (index > this.capacity) {
        panic("index must >= 0 and <= capacity")
    }
    //缓存要删除的元素
    removeElement := this.data[index]
    //删除位置及其之后的位置,需要向前移动
    for i:=index; i<this.size-1; i++ {
        this.data[i] = this.data[i+1]
    }
    //数组规则减少
    this.size--
    //清理最后一个元素
    this.data[this.size] = nil
    //缩容
    if (this.size < len(this.data)/4) && (len(this.data)/2 != 0) {
        this.resize(len(this.data)/2)
    }
    return removeElement
}

//改
func (this *array) set(index int, element interface{}) {
    //合法性判断
    if (index < 0) || (index > this.capacity) {
        panic("index must >= 0 and <= capacity")
    }
    //赋值
    this.data[index] = element
}

//查
func (this *array) get(index int) interface{} {
    //合法性判断
    if (index < 0) || (index > this.capacity) {
        panic("index must >= 0 and <= capacity")
    }
    return this.data[index]
}

//测试代码
func main() {
    testArr := getArray(10)
    for i:=0; i<10; i++ {
        testArr.add(0, i)
    }
    for i:=0; i<10; i++ {
        testArr.remove(0)
    }
    testArr.set(0, 1)
    testArr.set(1, 2)
    r := testArr.get(1)
    fmt.Println(r)
}
```

参考：https://segmentfault.com/a/1190000015680429
