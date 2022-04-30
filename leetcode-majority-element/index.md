# LeetCode : majority-element

```
package main
import "fmt"

func main() {
    fmt.Println(majorityElement([]int{1,2,3,1,1,1,1,1,1}))
}

func majorityElement(nums []int) int {
    m := make(map[int]int)
    for _,num := range nums {
        m[num]++
    }
    for num,count := range m {
        if (count > len(nums)/2) {
            return num
        }
    } 
    return 0
}

```

