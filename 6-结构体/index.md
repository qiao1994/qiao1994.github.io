# 6.结构体

# 结构体用”组合“的方式实现了类似其他语言中的”继承“

这里和其他语言的继承很相似，所以不再记录

# 值方法和指针方法

## 不同点

* 传入内容
  * 值方法的接收者是该方法所属的那个类型值的一个副本。我们在该方法内对该副本的修改一般都不会体现在原值上，除非这个类型本身是某个引用类型（比如切片或字典）的别名类型。
  * 而指针方法的接收者，是该方法所属的那个**基本类型值的指针值的一个副本**。我们在这样的方法内对该副本指向的值进行修改，就一定会体现在原值上。
* 方法集合
  * 一个自定义**数据类型的方法集合**中仅会包含它的所有值方法，而该类型的**指针类型的方法集合**却囊括了前者的所有方法，包括所有值方法和所有指针方法。
  * 严格来讲，我们在这样的基本类型的值上只能调用到它的值方法。但是，Go语言会适时地为我们进行自动地转译，使得我们在这样的值上也能调用到它的指针方法。
  
    > 比如，在`Cat`类型的变量`cat`之上，之所以我们可以通过`cat.SetName("monster")`修改猫的名字，是因为Go语言把它自动转译为了`(&cat).SetName("monster")`，即：先取`cat`的指针值，然后在该指针值上调用`SetName`方法。
* 和接口的关系
  * 一个类型的方法集合中有哪些方法与它能实现哪些接口类型是息息相关的。如果一个基本类型和它的指针类型的方法集合是不同的，那么它们具体实现的接口类型的数量就也会有差异，除非这两个数量都是零。
  
    > 比如，一个指针类型实现了某某接口类型，但它的基本类型却不一定能够作为该接口的实现类型。

## 举个栗子

```go
package main
import "fmt"

type Cat struct {
	name           string // 名字。
	scientificName string // 学名。
	category       string // 动物学基本分类。
}
//返回一个Cat结构体的名字叫New的函数
func New(name, scientificName, category string) Cat {
	return Cat{
		name:           name,
		scientificName: scientificName,
		category:       category,
	}
}
//Cat指针类型的一个SetName方法，给指针的副本做修改
func (cat *Cat) SetName(name string) {
	cat.name = name //cat是指针类型
}
//Cat类型的一个SetNameOfCopy方法，给值的副本做修改
func (cat Cat) SetNameOfCopy(name string) {
	cat.name = name //cat是值类型
}
//Cat类型的一个get方法，获取对应字段的值
func (cat Cat) Name() string {
	return cat.name
}
func (cat Cat) ScientificName() string {
	return cat.scientificName
}
func (cat Cat) Category() string {
	return cat.category
}
//Cat类型的string方法
func (cat Cat) String() string {
	return fmt.Sprintf("%s (category: %s, name: %q)",
		cat.scientificName, cat.category, cat.name)
}

func main() {
	cat := New("little pig", "American Shorthair", "cat")
	cat.SetName("monster") // (&cat).SetName("monster") 这里go语言做了自动转译，把基本类型转为了指针类型，修改有效
	fmt.Printf("The cat: %s\n", cat)
	cat.SetNameOfCopy("little pig") //对值的副本做修改，无效
	fmt.Printf("The cat: %s\n", cat)

	type Pet interface {
		SetName(name string)
		Name() string
		Category() string
		ScientificName() string
	}
	_, ok := interface{}(cat).(Pet)
	fmt.Printf("Cat implements interface Pet: %v\n", ok)  //false cat的基本类型没有实现SetName
	_, ok = interface{}(&cat).(Pet)
	fmt.Printf("*Cat implements interface Pet: %v\n", ok)//true cat的指针类型实现了SetName
}
```
