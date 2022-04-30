# LeetCode : valid-anagram

```
package main
import "fmt"
import "reflect"

func main() {
    fmt.Println(isAnagram("abc", "cbad"))
}

func isAnagram(s string, t string) bool {
    ms := make(map[rune]int)
    mt := make(map[rune]int)

    for _,sitem := range s {
        ms[sitem]++
    }

    for _,titem := range t {
        mt[titem]++
    }

    eq := reflect.DeepEqual(ms, mt)
    if eq {
        return true
    } else {
        return false
    }
}

```

