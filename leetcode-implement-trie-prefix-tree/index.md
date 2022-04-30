# LeetCode : implement-trie-prefix-tree

```
package main
import "fmt"

type TrieNode struct {
    children map[rune]*TrieNode
    isEnd bool
}

type Trie struct {
    root TrieNode
}


/** Initialize your data structure here. */
func Constructor() Trie {
    trie := Trie{TrieNode{make(map[rune]*TrieNode), false}}
    return trie
}

func main() {
    obj := Constructor();
    obj.Insert("abc");
    rs := obj.Search("abc")
    fmt.Println(rs)
}

/** Inserts a word into the trie. */
func (this *Trie) Insert(word string)  {
    node := &this.root
    var index int
    var char rune
    for index, char = range word {
        if _, ok := node.children[char]; (!ok) {
            node.children[char] = &TrieNode{make(map[rune]*TrieNode), false}
        }
        if (index < len(word) - 1) { 
            node = node.children[char]
        }
    }
    node.isEnd = true
}

func (this *Trie) Search(word string) bool {
    node := &this.root
    for index, char := range word {
        if _, ok := node.children[char]; (!ok) {
            return false
        } else {
            if (index < len(word) - 1) {
                node = node.children[char]
            }
        }
    }
    return node.isEnd
}


func (this *Trie) StartsWith(prefix string) bool {
    node := &this.root
    for _, char := range prefix {
        if _, ok := node.children[char]; (!ok) {
            return false
        } else {
            node = node.children[char]
        }
    }
    return true
}

```

