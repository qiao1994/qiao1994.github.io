# LeetCode : n-queens

```
package main
import "fmt"

func main() {
    fmt.Println(solveNQueens(2))
}

var result [][]int
var cols, pie, na map[int]int

func solveNQueens(n int) [][]string {
    cols = make(map[int]int)
    pie = make(map[int]int)
    na = make(map[int]int)
    dfs(n, 0, []int{})
    stringReuslt := [][]string{}
    for _,cols := range result { //index
        stringReusltSub := []string{}
        for _,col := range cols { //row
            line := ""
            for i := 0; i < n; i++ {
                if (i == col) {
                    line += "Q"
                } else {
                    line += "."
                }
            }
            stringReusltSub = append(stringReusltSub, line)
        }
        stringReuslt = append(stringReuslt, stringReusltSub)
    }
    return stringReuslt
}

func dfs(n int, row int, current []int) {
    //递归终止条件
    if row >= n {
        result = append(result, current)
        return
    }

    for col := 0; col < n; col++ {
        //当前位置不能放置皇后
        if cols[col] != 0 || pie[row+col] != 0 || na[row-col] != 0 {
            continue
        }

        //占领这个位置,加标志位
        cols[col] = 1
        pie[row+col] = 1
        na[row-col] = 1

        //占领了一个,向下探一行
        dfs(n, row+1, append(current, col))

        //回到这一层,删除标志
        cols[col] = 0
        pie[row+col] = 0
        na[row-col] = 0
    }
}

```

