# LeetCode : fibonacci-number

```
package main
import "fmt"

var memory []int

func main() {
    memory = make([]int, 4)
    fmt.Println(fibonacci3(3))
}

//递归计算(会有很多重复计算)
func fibonacci1(n int) int {
    if (n <= 1) {
        return n
    } else {
        return fibonacci1(n-1) + fibonacci1(n-2)
    }
}

//带记忆的递归计算
func fibonacci2(n int) int {
    if (n <= 1) {
        return n
    } else {
        if memory[n] == 0 {
            memory[n] = fibonacci2(n-1) + fibonacci2(n-2)
        }
    }
    return memory[n]
}

//自底向上的递推形式 f[n] = f[n-1] + f[n-2]
func fibonacci3(n int) int {
    memory := make([]int, n+1)
    if (n <= 1) {
        return n
    }
    memory[0] = 0
    memory[1] = 1
    for i:=2; i<=n; i++ {
        memory[i] = memory[i-1] + memory[i-2]
    }
    return memory[n]
}

```

