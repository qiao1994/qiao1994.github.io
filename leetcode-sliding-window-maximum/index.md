# LeetCode : sliding-window-maximum

```
package main
import "fmt"

func main() {
    //fmt.Println(maxSlidingWindow([]int{1,3,-1,-3,5,3,6,7}, 3))
    //fmt.Println(maxSlidingWindow([]int{1,-1}, 1))
    fmt.Println(maxSlidingWindow([]int{9,10,9,-1,-4,-8,2,-6}, 5))
}

func maxSlidingWindow(nums []int, k int) []int {
    if k == 0 || len(nums) == 0 {
        return []int{}
    }
    
    window := []int{}
    result := []int{}
    max := nums[0]
    maxIndex := 0
    var windowIndex,windowNum int

    for index,num := range nums {
        if num >= max {
            window = []int{num}
            max = num
            maxIndex = index
        } else {
            if len(window) < k {
                window = append(window, num)
            } else {
                window = append(window[1:], num)
                if index - k >= maxIndex {
                    //select the new max
                    max = num
                    maxIndex = index
                    for windowIndex,windowNum = range window {
                        if windowNum > max {
                            max = windowNum
                            maxIndex = index - (k - windowIndex)
                        }
                    }
                }
            } 
        }

        if index >= k-1 {
            result = append(result, max)
        }
    }

    return result
}

```

