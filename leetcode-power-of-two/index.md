# LeetCode : power-of-two

```
package main
import "fmt"

func main() {
    fmt.Println(isPowerOfTwo(10))
}

func isPowerOfTwo(n int) bool {
    return n!= 0 && n & (n-1) == 0
}
```

