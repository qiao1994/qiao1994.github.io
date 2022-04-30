# LeetCode : number-of-1-bits

```
package main
import "fmt"

func main() {
    fmt.Println(hammingWeight(10))
}

func hammingWeight(num uint32) int {
    if (num == 0) {
        return 0
    }
    count := 0
    for num & (num-1) != 0 {
        count++
        num = num & (num - 1)
    }
    count++
    return count
}

```

