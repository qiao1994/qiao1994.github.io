# LeetCode : binary-search

```
package main
import "fmt"

var data []int
var target int

func main() {
    data = []int{0,1,2,3,4,5,6,7,8,9}
    target = 6
    fmt.Println(binarySearch())
}

func binarySearch() int {
    return binarySearchHelp(0, len(data)-1);
}

func binarySearchHelp(left int, right int) int {
    mid := (left + right) / 2
    if (left < 0) || (right < 0) || (mid < 0) {
        return -1
    }
    if (data[mid] == target) {
        return mid
    } else if (data[mid] < target) {
        return binarySearchHelp(mid+1, right)
    } else {
        return binarySearchHelp(left, mid-1)
    }
}

```

