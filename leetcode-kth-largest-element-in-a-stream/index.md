# LeetCode : kth-largest-element-in-a-stream

```
package main
import "container/heap"
import "fmt"

// heap
type IntHeap []int
func (h IntHeap) Len() int            { return len(h) }
func (h IntHeap) Less(i, j int) bool  { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }

func (h *IntHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[0 : n-1]
    return x
}

func (h *IntHeap) Push(x interface{}) { 
    *h = append(*h, x.(int))
}


type KthLargest struct {
    vals IntHeap
    size int
}

//heap end

func Constructor(k int, nums []int) KthLargest {
    var right int
    if len(nums) > k {
        right = k
    } else {
        right = len(nums)
    }
    vals := nums[0:right]
    obj := KthLargest{vals, k}

    //init vals as a heap
    heap.Init(&obj.vals)

    //push other valus into obj
    for i := k; i < len(nums); i++ {
        heap.Push(&obj.vals, nums[i])
        heap.Pop(&obj.vals)
    }

    //return the obj
    return obj
}


func (this *KthLargest) Add(val int) int {
    heap.Push(&this.vals, val)
    for this.vals.Len() > this.size {
        heap.Pop(&this.vals)
    }
    return this.vals[0]
}


/**
 * Your KthLargest object will be instantiated and called as such:
 * obj := Constructor(k, nums);
 * param_1 := obj.Add(val);
 */

func main() {
    obj := Constructor(3, []int{4,5,2,8});
    fmt.Println(obj)
    fmt.Println(obj.Add(-6))
}

```

