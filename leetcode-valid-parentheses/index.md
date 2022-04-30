# LeetCode : valid-parentheses

```
package main
import "fmt"
import "strings"
import "container/list"

func main() {
    s := "("
    result := isValid(s)
    fmt.Println(result)
    
}

func isValid(s string) bool {
    //切数组
    items := strings.Split(s, "")
    //初始化栈
    stack := list.New()
    for _, item := range items {
        //左括号,入栈
        if (item == "(") || (item == "[") || (item == "{") {
            stack.PushBack(item)
            continue
        }
        //右括号,出栈
        if stack.Len() == 0 {
            return false
        }
        if (item == ")") {
            if stack.Back().Value == "(" {
                stack.Remove(stack.Back())
                continue
            } else {
                return false
            }
        }
        if (item == "]") {
            if stack.Back().Value == "[" {
                stack.Remove(stack.Back())
                continue
            } else {
                return false
            }
        }
        if (item == "}") {
            if stack.Back().Value == "{" {
                stack.Remove(stack.Back())
                continue
            } else {
                return false
            }
        }
        return false
    }
    //如果已完全匹配,则栈内剩余数量为0
    if (stack.Len() == 0) {
        return true
    } else {
        return false
    }
}

```

