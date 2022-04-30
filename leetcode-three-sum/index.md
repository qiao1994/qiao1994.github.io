# LeetCode : three-sum

```
package main
import "fmt"
import "sort"
import "strconv"

func main() {
    fmt.Println(threeSum([]int{-4,-2,-2,-2,0,1,2,2,2,3,3,4,4,6,6}))
}

func threeSum(nums []int) [][]int {
    result := [][]int{}
    set := make(map[int]int)
    used := make(map[string]int)
    for index,num := range nums {
        set[num] = index
    }
    
    for indexA,numA := range nums {
        for indexB,numB := range nums {
            numC := 0 - numA - numB
            indexC,ok := set[numC]
            if (indexA != indexB) && (indexA != indexC) && (indexB != indexC) && ok {
                resultItem := []int{numA, numB, numC}
                usedKey := ""
                sort.Ints(resultItem)
                for _,item := range resultItem {
                    usedKey += strconv.Itoa(item)
                }
                _,ok := used[usedKey]
                if !ok {
                    result = append(result, resultItem)
                    used[usedKey] = 0
                }
            }
        }
    }
    return result
}

```

