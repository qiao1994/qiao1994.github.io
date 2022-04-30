# LeetCode : generate-parentheses

```
package main
import "fmt"

func main() {
    fmt.Println(generateParenthesis(2))
}

var list []string

func generateParenthesis(n int) []string {
    list = []string{}
    gen(0, 0, n, "")
    return list
}

func gen(left, right,n int, result string) {
    if (left == n) && (right == n) {
        list = append(list, result)
        return
    }
    if left < n {
        gen(left+1, right, n, result + "(")
    }
    if left > right && right < n {
        gen(left, right+1, n, result + ")")
    }
}

```

