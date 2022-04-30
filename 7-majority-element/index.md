# 求大多数


## 思路

* 思路一：使用一个hash表，统计每个元素出现的次数
* 思路二：用一个变量result暂存当前的数字，遇到一个数字如果是result，就给count+1，否则给count-1；当count = 0时，更换result；这样如果一定有一个大多数，最终的reuslt中存的一定是所求

## 问题

思路很清晰，代码很简单，唯一的问题是这个思路二一般人真的是想不到...

## 代码

```go
package main
import "fmt"

func main() {
    nums := []int{3,2,3}
    r := majorityElement(nums)
    fmt.Println(r)
}

func majorityElement(nums []int) int {
    result, count := 0, 0
    for _, num := range nums {
        if count == 0 {
            result = num
            count++
        } else {
            if num == result {
                count++
            } else {
                count--
            }
        }
    }
    return result
}
```


