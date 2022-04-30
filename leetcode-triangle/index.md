# LeetCode : triangle

```
package main
import "fmt"

func main() {
    fmt.Println(minimumTotal([][]int{
        {2},
        {3,4},
        {6,5,7},
        {4,1,8,3},
    }))
}

func minimumTotal(triangle [][]int) int {
    max := len(triangle) - 1
    result := triangle
    for i:=max; i>=0; i-- {
        for j:=0; j<=max; j++ {
            //init bottom
            if (i == len(triangle) - 1) {
                result[i][j] = triangle[i][j]
            } else {
                if result[i+1][j] < result[i+1][j+1] {
                    result[i][j] = result[i+1][j] +  triangle[i][j]
                } else {
                    result[i][j] = result[i+1][j+1] +  triangle[i][j]
                }
            } 
        }
        max = max - 1
    } 
    return result[0][0]
}

```

