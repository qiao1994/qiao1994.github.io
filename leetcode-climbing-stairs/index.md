# LeetCode : climbing-stairs

```
package main
import "fmt"
func main() {
    fmt.Println(climbStairs(3))
}

func climbStairs(n int) int {
    memory := make([]int, n+1)
    memory[0] = 1
    memory[1] = 1
    for i:=2; i<=n; i++ {
        memory[i] = memory[i-1] + memory[i-2]
        //这里可以优化,memory不需要n的空间，只需要三个位置即可
    }
    return memory[n]
}

```

