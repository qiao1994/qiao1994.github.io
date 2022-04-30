# 3 sum


## 思路

### 2 sum的三种解法

* 暴力两重循环 O(n^2)
* map+一重循环 O(n) + O(n)
* 排序+一重循环 O(nlogn)

### 3 sum的三种解法

- 暴力三重循环 O(n^3)
- map+两重循环 O(n) + O(n^2)
- 排序+两重循环 O(n^2)

## 问题

核心问题是去重

* 相同的值需要快进，避免重复
* 遍历的时候需要从最外层指针的右侧开始夹逼，避免重复
* 先快进，再++ 保证比较过后再快进(如果先++后快进，更容易抵达j>=k导致循环退出，错过结果)

## 代码

```go
package main
import "fmt"

func main() {
    r := threeSum([]int{-2,0,1,1,2}, 0)
    fmt.Println(r)
}

/*
核心问题就是去重
1.相同的值需要快进，避免重复
2.遍历的时候需要从最外层指针的右侧开始夹逼，避免重复
3.先快进，再++ 保证比较过后再快进(如果先++后快进,更容易抵达j<k导致循环退出)
*/
func threeSum(nums []int, target int) [][]int {
    quickSort(nums, 0, len(nums) - 1)
    fmt.Println(nums)
    result := [][]int{}
    for i:=0; i<len(nums); i++ {
        j, k := i + 1, len(nums) - 1
        fmt.Println("i+++")
        for j < k {
            fmt.Printf("1:i:%d,j:%d,k:%d;nums[i]:%d,nums[j]:%d,nums[k]:%d\n", i, j, k, nums[i], nums[j], nums[k])
            if nums[i] + nums[j] + nums[k] == target && i != j && j != k && i != k {
                result = append(result, []int{nums[i], nums[j], nums[k]})
                for j < k && nums[j] == nums[j+1] {
                    fmt.Println("j++")
                    j++
                }
                for j < k && nums[k] == nums[k-1] {
                    fmt.Println("k--")
                    k--
                }
                for i < len(nums) - 1 && nums[i] == nums[i+1] {
                    fmt.Println("i++")
                    i++
                }
                j++
                k--
            } else if nums[i] + nums[j] + nums[k] < target {
                    fmt.Println("j++")
                j++
            } else {
                k--
                fmt.Println("k--")
            }
            fmt.Printf("2:i:%d,j:%d,k:%d;nums[i]:%d,nums[j]:%d,nums[k]:%d\n", i, j, k, nums[i], nums[j], nums[k])
        }
    }
    return result
}
//快速排序
func quickSort(data []int, left, right int) {
    if left >= right {
        return ;
    }

    flag := data[left];
    i, j := left, right;

    for i < j {
        //←
        for i < j && data[j] >= flag {
            j--;
        }
        if i < j  {
            data[i] = data[j];
            i++;
        }
        //→
        for i < j && data[i] < flag {
            i++;
        }
        if i < j {
            data[j] = data[i];
            j--;
        }
    }
    data[i] = flag;
    quickSort(data, left, i);
    quickSort(data, i+1, right);
}
```


