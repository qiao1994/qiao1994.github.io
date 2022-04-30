# LeetCode : best-time-to-buy-and-sell-stock-ii

```
package main
import "fmt"

func main(){
    fmt.Println(maxProfit([]int{7,1,5,3,6,4}))
}

func maxProfit(prices []int) int {
    profit := 0
    for index,price := range prices {
        if index > 0 && price > prices[index-1] {
            profit = profit + price - prices[index-1]
        }
    }
    return profit
}

```

