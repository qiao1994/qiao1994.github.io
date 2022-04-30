# 合并两个有序数组


如果有的选，选一个小的；如果没的选，把另外一个遍历完。

```go
package main
import "fmt"

//合并两个有序数组
func merge(array1, array2 []int) []int {
    result := []int{}
    i, j := 0, 0
    for (i <= len(array1)-1) || (j <= len(array2)-1)  {
        //两个数组如果其中一个已遍历完成,持续累加另一个
        if i > (len(array1)-1) {
            result = append(result, array2[j])
            j++
            continue
        }
        if j > (len(array2)-1) {
            result = append(result, array1[i])
            i++
            continue
        }
        //从个数组中选择一个小的
        if array1[i] < array2[j] {
            result = append(result, array1[i])
            i++
        } else {
            result = append(result, array2[j])
            j++
        }
    }
    return result
}

//测试
func main() {
    result := merge([]int{1, 3, 5, 7, 9}, []int{2, 4, 6, 8, 10})
    fmt.Println(result)
}
```


