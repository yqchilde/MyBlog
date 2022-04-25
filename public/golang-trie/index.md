# Golang之Trie写法 && sort.Interface使用

<!--more-->

### Trie

Trie又称字典树，是N叉树的一种特殊形式。是一种存储大量字符串的树形数据结构，相当于`HashMap`，在存储单词中又比`HashMap`节省了很多的内存空间。`trie`树的根节点不存储内容，内容都在孩子节点上。

![](https://pic.yqqy.top/blog/20200328174533.png)

这是一个保存了8个单词的字典树的结构，8个单词分别是"A", "to", "tea", "ted", "ten", "i", "in", "inn"。每条从根节点到叶子节点的路径都构成了单词（有的不需要走到叶子节点也是单词，比如`i`, `in`），从根节点出发的路径都是给定单词列表的某个/某些单词的前缀，反之如果在某个字符串中没有出现在这棵树的路径上，就说明组不成单词。

> 通常Trie树也会用在搜索引擎上，就是输入某些词，就会扩展出一些模糊词汇
>
> Golang 实现的写法如下：

```go
type Trie struct {
	children [26]*Trie
	isWord bool
}

// Insert
func (t *Trie) Insert(word string)  {
	cur := t
	for k, v := range word {
		c := v - 'a'
		if cur.children[c] == nil {
			cur.children[c] = &Trie{}
		}
		cur = cur.children[c]
		// end
		if k == len(word) -1 {
			cur.isWord = true
		}
	}
}

// Search
func (t *Trie) Search(word string) bool  {
	cur := t
	for _, v := range word {
		c := v - 'a'
		if cur.children[c] == nil {
			return false
		}
		cur = cur.children[c]
	}
	return cur.isWord
}

// StartWith
func (t *Trie) StartWith(prefix string) bool {
	cur := t
	for _, v := range prefix {
		c := v - 'a'
		if cur.children[c] == nil {
			return false
		}
		cur = cur.children[c]
	}
	return true
}

func main() {
    trie := new(Trie)

	trie.Insert("inn")
	fmt.Println(trie.Search("inn"))   // true
	fmt.Println(trie.Search("in"))    // false
	fmt.Println(trie.StartWith("in")) // true
	fmt.Println(trie.StartWith("ia")) // false
}
```

### sort.Interface

> 关于自定义排序，用这个就可以了

在官方库`sort.go`中关于`Interface`定义如下：

```go
// A type, typically a collection, that satisfies sort.Interface can be
// sorted by the routines in this package. The methods require that the
// elements of the collection be enumerated by an integer index.
type Interface interface {
	// Len is the number of elements in the collection.
	Len() int
	// Less reports whether the element with
	// index i should sort before the element with index j.
	Less(i, j int) bool
	// Swap swaps the elements with indexes i and j.
	Swap(i, j int)
}
```

所以我们只需要实现上面三个方法就可以了，如下是一个按照数组中字符串长度排序的一个例子

```go
package main

import (
	"fmt"
	"sort"
)

type MySliceSort []string

func (m MySliceSort) Len() int {
	return len(m)
}

func (m MySliceSort) Less(i, j int) bool {
	return len(m[i]) > len(m[j])
}

func (m MySliceSort) Swap(i, j int) {
	m[i], m[j] = m[j], m[i]
}

func main() {
	sliceSort := MySliceSort{
		"这是五个字",
		"我要变六个字",
		"三个字",
	}
	sort.Sort(sliceSort)
	fmt.Println(sliceSort) // [我要变六个字 这是五个字 三个字]
}
```
