# LeetCode : sqrtx

```
package main
import "fmt"

func main() {
    fmt.Println(mySqrt(1))
}

func mySqrt(x int) int {
    if (x == 1) {
        return 1
    }
    return mySqrtHelp(0, x, x)
}

func mySqrtHelp(left,right,x int) int {
    if (right - left) <= 1 {
        return left
    }
    mid := (left + right) / 2
    if mid * mid == x {
        return mid
    } else if (mid * mid > x) {
        return mySqrtHelp(left, mid, x)
    } else {
        return mySqrtHelp(mid, right, x)
    }
}

```

