# Leetcode:870. 优势洗牌(田忌赛马)-贪心+双指针


题目链接：https://leetcode-cn.com/problems/advantage-shuffle/

### 初步思路

对nums1排序，对于nums2的每个元素，都尽量尝试用nums1中尽量小的元素解决，就得到了最优势的解法。

但是这种方法的时间复杂度是O(N^2)，果然超时

![image-20220502195150590](https://qiao1994.github.io/images/Leetcode/image-20220502195150590.png)

```go
func advantageCount(nums1 []int, nums2 []int) []int {
    result := make([]int, len(nums1))
    for i, _ := range result {
        result[i] = -1
    }
    sort.Ints(nums1)

    for i, num2 := range nums2 {
        for j, num1 := range nums1 {
            if num1 > num2 {
                result[i] = num1
                nums1[j] = -1
                break
            }
        }
    }

    for i, item := range result {
        if item == -1 {
            for j, num1 := range nums1 {
                if num1 != -1 {
                    result[i] = num1
                    nums1[j] = -1
                    break
                }
            }
        }
    }

    return result
}
```

### nlog(n)的方法：贪心+双指针

把nums1、nums2升序排列(nums2需要记录位置，因为最终的result中需要还原位置)，遍历nums1和nums2：当nums1中最小值能够干掉nums2的最小值时，把对应的元素放到nums2最小值的对应位置，干不掉时，直接把nums1中的最小值放到nums2最大值的对应位置(因为nums1中的这个值连nums2中最小的值都打不过，所以放弃抵抗，直接让它面对nums2中最强的)。

```go
type pair struct {
    k, v int
}

func advantageCount(nums1 []int, nums2 []int) []int {
    // nums1升序排列
    sort.Ints(nums1)
    // nums2包含位置排列
    pairs2 := make([]pair, len(nums2))
    for k, v := range nums2 {
        pairs2[k].k = k
        pairs2[k].v = v
    }
    sort.Slice(pairs2, func(i, j int) bool { return pairs2[i].v < pairs2[j].v })

    result := make([]int, len(nums1))
    min, max := 0, len(nums2) - 1 // 两个指针分别指向nums2的最小值和最大值位置

    for _, v1 := range nums1 {
        if v1 > pairs2[min].v {
            // 能干掉，直接放入该位置，继续匹配下一个位置
            result[pairs2[min].k] = v1
            min++
        } else {
            // 干不掉，直接放入nums2的最大位置，继续尝试用nums1中更大的值干掉这个位置的元素
            result[pairs2[max].k] = v1
            max--
        }
    }

    return result
}
```
