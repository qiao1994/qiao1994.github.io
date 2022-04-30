# 寻找第一个缺失的正数


## 思路

* 思路一：排序，然而不能满足空间复杂度的要求
* 思路二：把每个位置的**有效**num，与num-1位置的元素交换，然后遍历处理后的数组，第一个元素值不等于索引值+1的位置，即为缺失的第一个正数

## 问题

* **有效**：大于0，且num小于等于数组容量的元素是有效元素；
* 交换时把指针位置交换到了其他位置，需要继续再该位置交换，直到该位置没有可交换的元素才能移动；否则可能会出现被换到这个位置的元素需要被处理，但是没有被处理；case:[3,4,-1,1]

## 代码

```go
package main
import "fmt"

func main() {
    nums := []int{3,4,-1,1}
    r := firstMissingPositive(nums)
    fmt.Println(r)
}


func firstMissingPositive(nums []int) int {
    for i:=0; i<len(nums); {
        num := nums[i]
        if num > 0 && num <= len(nums) && nums[num-1] != num {
            nums[num-1], nums[i] = nums[i], nums[num-1]
        } else {
            i++
        }
    }
    for i, num := range nums {
        if num != i+1 {
            return i+1
        }
    }
    return len(nums) + 1
}
```


