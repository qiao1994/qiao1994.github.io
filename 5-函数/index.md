# 5.函数

# 函数是“一等公民”

**函数不但可以用于封装代码、分割功能、解耦逻辑，还可以化身为普通的值**，在其他函数间传递、赋予变量、做类型判断和转换等等，就像切片和字典的值那样

函数类型属于引用类型，它的值可以为`nil`，这种类型的零值也是`nil`。

```go
type Printer func(contents string) (n int, err error) //声明函数类型

//函数printToStd的签名与Printer的是一致的,printToStd是Printer的一个实现
func printToStd(contents string) (bytesNum int, err error) {
    return fmt.Println(contents)
}

func main() {
    var p Printer //声明了一个Printer类型的变量p
    p = printToStd //把函数printToStd复制给变量p
    p("something") //执行p
}
```

# 高阶函数

## 高阶函数的特点

* 接受其他的函数作为参数传入
* 把其他的函数作为结果返回

```go
type operate func(x, y int) int //声明一个函数类型

//calculate会根据传入的两个数字和对应的操作函数执行相应的操作
func calculate(x int, y int, op operate) (int, error) {
    if op == nil {
        return 0, errors.New("invalid operation")
    }
    return op(x, y), nil
}

//创建一个operate类型的匿名函数
op := func(x, y int) int {
    return x + y
}
calculate(1, 2, op) //调用
```

## 高阶函数可以用来实现闭包

```go
func genCalculator(op operate) calculateFunc {
    return func(x int, y int) (int, error) {
        if op == nil {
            return 0, errors.New("invalid operation")
        }
        return op(x, y), nil
    }
}
```

# 传入函数的参数

* **所有传给函数的参数值都会被复制，函数在其内部使用的并不是参数值的原值，而是它的副本**

  > 一言不合就是副本

* 对于引用类型，比如：切片、字典、通道，像上面那样复制它们的值，只会拷贝它们本身而已，并不会拷贝它们引用的底层数据

  > 以切片值为例，如此复制的时候，只是拷贝了它指向底层数组中某一个元素的指针，以及它的长度值和容量值，而它的底层数组并不会被拷贝。

