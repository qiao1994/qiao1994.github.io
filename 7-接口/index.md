# 7.接口

# 接口的基本性质

```go
package main
import "fmt"

//定义一个接口Pet，接口里的内容是接口方法的定义，只要一个数据类型的方法集合中有这3个方法，那么它就一定是Pet接口的实现类型
type Pet interface {
	SetName(name string)
	Name() string
	Category() string
}

//结构体Dog相关定义
type Dog struct {
	name string
}

func (dog *Dog) SetName(name string) {
	dog.name = name
}

func (dog Dog) Name() string {
	return dog.name
}

func (dog Dog) Category() string {
	return "dog"
}
//经过上述定义，得到一个Dog类型的结构体，实现了Name()、Category()方法；一个Dog的指针类型*Dog，实现了SetName()方法

func main() {
	// 示例1。
	dog := Dog{"little pig"}
	_, ok := interface{}(dog).(Pet)
    fmt.Printf("Dog implements interface Pet: %v\n", ok) //false，因为Dog没有实现setName()方法
	_, ok = interface{}(&dog).(Pet)
	fmt.Printf("*Dog implements interface Pet: %v\n", ok) //true,因为*Dog实现了setName()方法
	fmt.Println()

	// 示例2。
	var pet Pet = &dog //此时pet的动态类型是*Dog；动态值是&dog
	fmt.Printf("This pet is a %s, the name is %q.\n",
		pet.Category(), pet.Name())
}
```

# 当我们为一个接口变量赋值时会发生什么

```go
package main
import "fmt"

type Pet interface {
	Name() string
	Category() string
}

type Dog struct {
	name string
}

func (dog *Dog) SetName(name string) {
	dog.name = name
}

func (dog Dog) Name() string {
	return dog.name
}

func (dog Dog) Category() string {
	return "dog"
}

func main() {
	// 示例1。
	dog := Dog{"little pig"} //初始化一个Dog
	fmt.Printf("The dog's name is %q.\n", dog.Name())
	var pet Pet = dog //赋值给pet接口
    dog.SetName("monster") //调用dog的指针的方法，设置name的值；这个操作完成后dog的Name已经变化了，但是pet的Name没有变化(因为pet拿到的是dog的副本，不是原值，所以dog原值的指针只能修改dog原值的内容)
	fmt.Printf("The dog's name is %q.\n", dog.Name()) //monster
	fmt.Printf("This pet is a %s, the name is %q.\n",
		pet.Category(), pet.Name()) //原来的值
	fmt.Println()
    //事实上，pet包含了dog的副本，但是不只是dog的副本；它是一种叫iface的实例，它有两个指针，一个指向类型信息，一个指向动态值。

	// 示例2。
	dog1 := Dog{"little pig"}
	fmt.Printf("The name of first dog is %q.\n", dog1.Name()) //pig
	dog2 := dog1
	fmt.Printf("The name of second dog is %q.\n", dog2.Name()) //pig
	dog1.name = "monster" //只修改了dog1的name，dog2不变
	fmt.Printf("The name of first dog is %q.\n", dog1.Name()) //monster
	fmt.Printf("The name of second dog is %q.\n", dog2.Name()) //pig
	fmt.Println()

	// 示例3。
	dog = Dog{"little pig"}
	fmt.Printf("The dog's name is %q.\n", dog.Name()) //pig
	pet = &dog //pet的动态类型是*Dog
	dog.SetName("monster") //dog的值发生了变化，因为pet的值是dog指针的副本，所以pet的值也发生了变化
	fmt.Printf("The dog's name is %q.\n", dog.Name())
	fmt.Printf("This pet is a %s, the name is %q.\n",
		pet.Category(), pet.Name()) //monster
}
```

# 接口变量的值在什么情况下才真正为`nil`？

只有声明但不初始化，或者显示的赋值为nil时候，接口变量的值才真正为nil；

如果把一个值为nil的结构体变量赋值给接口变量，这个接口变量的值并不是真正的nil；因为它的另外一个指针保存了这个结构体变量的类型。

# 接口的组合

因为接口只有方法定义，无法覆盖，直接嵌套即可

go语言团队推荐开发者使用小接口的方式，便于接口的自由组合

```go
type Animal interface {
	ScientificName() string
	Category() string
}

type Pet interface {
	Animal
	Name() string
}
```
