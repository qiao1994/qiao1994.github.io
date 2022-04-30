# LeetCode : two-sum

```
package main
import "fmt"

func main() {
    fmt.Println(twoSum([]int{2,7,11,15}, 9))
}

func twoSum(nums []int, target int) []int {
    set := make(map[int]int)
    for index,num := range nums {
        set[num] = index 
    }

    for index,num := range nums {
        _, ok := set[target - num]
        if ok {
            if index != set[target - num] {
                return []int{index, set[target - num]}
            }
        }
    }
    return []int{}
}

```

