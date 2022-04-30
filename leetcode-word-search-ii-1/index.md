# LeetCode : word-search-ii-1

```
package main
import "fmt"

var result []string

func main() {
    /*
    board := [][]byte{
        {'o','a','a','n'},
        {'e','t','a','e'},
        {'i','h','k','r'},
        {'i','f','l','v'},
    }
    */
    board := [][]byte{
        {'a', 'b'},
    }
    words := []string{
        "ba",
    }
    rs := findWords(board, words)
    fmt.Println(rs)
}

func findWords(board [][]byte, words []string) []string {
    result = []string{}
    for _,word := range words {
        dfs(board, word, 0, 0, 0)
    }
    //dictinct
    tempMap := map[string]int{}
    for _, item := range result {
        tempMap[item] = 0
    }
    realResult := []string{}
    for index, _ := range tempMap {
        realResult = append(realResult, index)
    }
    return realResult
}

func dfs(board [][]byte, word string, pos, x, y int) bool {
    fmt.Println("find:", string(word[pos]), ",x:", x, ",y:", y)
    //check if x or y out of range
    maxX := len(board)  - 1
    maxY := len(board[0])  - 1
    if x > maxX || y > maxY {
        return false
    }
    //find strat point
    if (board[x][y] == word[pos]) {
        if pos == len(word) - 1 {
            // success
            result = append(result, word)
            return true
        }
        // go on recurision
        if (x > 0) {
            if dfs(board, word, pos+1, x-1, y) {
                return true
            }
        }
        if (y > 0) {
            if dfs(board, word, pos+1, x, y-1) {
                return true
            }
        }
        if x < maxX {
            if dfs(board, word, pos+1, x+1, y) {
                return true
            }
        }
        if y < maxY {
            if dfs(board, word, pos+1, x, y+1) {
                return true
            }
        }
    } else {
        // go on recurision
        if (x > 0) {
            if dfs(board, word, pos, x-1, y) {
                return true
            }
        }
        if (y > 0) {
            if dfs(board, word, pos, x, y-1) {
                return true
            }
        }
        if x < maxX {
            if dfs(board, word, pos, x+1, y) {
                return true
            }
        }
        if y < maxY {
            if dfs(board, word, pos, x, y+1) {
                return true
            }
        }
    }
    return false
}

```

