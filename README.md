# 🚀 Go + DSA + LeetCode — Zero to Google Interview

> A single, self-contained study guide. Learn Go from scratch, master data structures & algorithms, then crush LeetCode and a Google interview — all in Go.

**How to read this document**
- If you're brand new: read Part 1 (Go basics) end-to-end and *type out* every snippet.
- If you know Go: skim Part 1, then live in Parts 3–5.
- Treat the patterns in Part 4 as the real curriculum. ~90% of interview problems are variations of ~15 patterns.

---

## 📑 Table of Contents

1. [Part 0 — The Roadmap & Study Plan](#part-0--the-roadmap--study-plan)
2. [Part 1 — Go Fundamentals](#part-1--go-fundamentals)
3. [Part 2 — Go for Interviews (the toolkit)](#part-2--go-for-interviews-the-toolkit)
4. [Part 3 — Data Structures in Go](#part-3--data-structures-in-go)
5. [Part 4 — The 15 Patterns + LeetCode Problems](#part-4--the-15-patterns--leetcode-problems)
6. [Part 5 — Google Interview Playbook](#part-5--google-interview-playbook)
7. [Appendix — Complexity Cheat Sheet](#appendix--complexity-cheat-sheet)

---

## Part 0 — The Roadmap & Study Plan

### The honest truth about Google interviews
Google's coding rounds test **problem-solving under pressure**, not memorization. You'll get 1–2 problems in 45 minutes each. They watch *how you think*: clarifying the problem, choosing a data structure, reasoning about complexity, writing clean code, and testing it. Knowing the patterns below lets you recognize a problem in 60 seconds instead of staring blankly.

### An 8-week plan (≈2 hrs/day)

| Week | Focus | Goal |
|------|-------|------|
| 1 | Go syntax + Part 1 & 2 | Write Go fluently without lookups |
| 2 | Arrays, Strings, Hashing, Two Pointers | 20 easy problems |
| 3 | Sliding Window, Stack, Linked List | 20 problems |
| 4 | Binary Search, Trees, BFS/DFS | 25 problems |
| 5 | Heaps, Backtracking, Graphs | 25 problems |
| 6 | Dynamic Programming | 25 problems |
| 7 | Greedy, Intervals, Tries, Bit tricks | 20 problems |
| 8 | Mock interviews + review weak spots | 10 mocks |

**Rule:** if you can't solve a problem in 30–40 min, read the solution, understand it deeply, then re-solve it from scratch 2 days later. Re-solving is where learning happens.

### What "done" looks like
You can look at any new problem, say "this is a sliding-window problem," and produce working Go in 20 minutes while talking through your reasoning.

---

## Part 1 — Go Fundamentals

Go is small on purpose. You can learn the entire language for interviews in a day. Here's everything you need.

### 1.1 Hello, World & program structure

```go
package main // every executable starts here

import "fmt" // standard library import

func main() {
    fmt.Println("Hello, World")
}
```

Run it:
```bash
go run main.go        # compile + run
go build              # produce a binary
```

### 1.2 Variables & constants

```go
var x int = 10        // explicit type
var y = 10            // type inferred
z := 10               // short form — ONLY inside functions
const Pi = 3.14159    // compile-time constant

var a, b = 1, "two"   // multiple, mixed types

// Zero values (Go has no "undefined"):
var i int       // 0
var f float64   // 0.0
var s string    // ""  (empty string)
var b2 bool     // false
var p *int      // nil
```

> **Interview tip:** `:=` is your default inside functions. Forgetting `:=` vs `=` is the #1 beginner Go bug.

### 1.3 Basic types

```go
int, int8, int16, int32, int64      // int is 64-bit on modern machines
uint, uint8 (byte), uint32, uint64
float32, float64
bool
string                               // immutable, UTF-8
rune                                 // alias for int32, a Unicode code point
```

Conversions are always **explicit** — Go never converts for you:
```go
i := 42
f := float64(i)
back := int(f)
```

### 1.4 Strings, bytes, and runes (interview-critical)

A Go string is an immutable read-only slice of bytes.

```go
s := "héllo"
len(s)          // 6  — BYTES, not characters (é is 2 bytes in UTF-8)
s[0]            // 104 — a byte (uint8), not "h" as a string

// Iterate by BYTES:
for i := 0; i < len(s); i++ {
    b := s[i] // byte
}

// Iterate by RUNES (characters):
for i, r := range s {
    fmt.Printf("%d: %c\n", i, r) // r is a rune
}

// Convert to a mutable form:
bs := []byte(s)   // for ASCII / byte manipulation
rs := []rune(s)   // for Unicode-safe character indexing
rs[0] = 'H'
s = string(rs)
```

> For LeetCode, inputs are usually ASCII, so `[]byte(s)` is the fast, idiomatic choice. Use `[]rune` only when Unicode matters.

The `strings` and `strconv` packages do the heavy lifting:
```go
import ("strings"; "strconv")

strings.Contains("seafood", "foo")   // true
strings.Split("a,b,c", ",")          // ["a" "b" "c"]
strings.Join([]string{"a","b"}, "-") // "a-b"
strings.ToLower("ABC")               // "abc"
strings.Repeat("ab", 3)              // "ababab"
strings.TrimSpace("  hi  ")          // "hi"

n, _ := strconv.Atoi("123")          // string -> int
str := strconv.Itoa(123)             // int -> string

var sb strings.Builder               // efficient string concatenation
sb.WriteString("foo")
sb.WriteByte('!')
result := sb.String()
```

> **Performance trap:** building strings with `s += x` in a loop is O(n²) because strings are immutable. Use `strings.Builder`.

### 1.5 Control flow

```go
// if — condition needs no parentheses; braces required
if x > 0 {
    // ...
} else if x == 0 {
    // ...
} else {
    // ...
}

// if with a short statement (scoped to the if/else):
if v, err := doThing(); err == nil {
    use(v)
}

// the only loop keyword is `for`
for i := 0; i < n; i++ { }      // classic
for cond { }                    // while-style
for { break }                   // infinite

// range over collections
for i, v := range slice { }     // index, value
for k, v := range myMap { }     // key, value
for _, v := range slice { }     // ignore index with _

// switch — no fallthrough by default, no break needed
switch day {
case "Sat", "Sun":
    fmt.Println("weekend")
default:
    fmt.Println("weekday")
}

// switch with no condition = clean if/else chain
switch {
case x < 0:
    // ...
case x == 0:
    // ...
}
```

### 1.6 Functions

```go
func add(a, b int) int {        // shared type: (a, b int)
    return a + b
}

// multiple return values — Go's signature feature
func divmod(a, b int) (int, int) {
    return a / b, a % b
}
q, r := divmod(17, 5)

// named returns
func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return // "naked" return uses named values
}

// variadic
func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}
sum(1, 2, 3)

// functions are values / closures
adder := func(x int) func(int) int {
    return func(y int) int { return x + y }
}
add5 := adder(5)
add5(3) // 8
```

### 1.7 The error pattern

Go has no exceptions for normal control flow. Functions return an `error` as the last value.

```go
import "errors"

func sqrt(x float64) (float64, error) {
    if x < 0 {
        return 0, errors.New("cannot sqrt negative")
    }
    return math.Sqrt(x), nil
}

v, err := sqrt(-1)
if err != nil {
    // handle it
}
```

> In LeetCode you rarely need errors, but you'll see `_` used to discard them: `n, _ := strconv.Atoi(s)`.

### 1.8 `defer`, `panic`, `recover`

```go
func read() {
    f := open()
    defer f.close() // runs when the function returns, in LIFO order
    // ...use f...
}
```
`panic`/`recover` exist but are rarely used in interviews — mention them only if asked.

---

## Part 2 — Go for Interviews (the toolkit)

This is the 20% of Go you'll use in 80% of problems. Master these cold.

### 2.1 Slices — the workhorse

A slice is a dynamic, growable view over a backing array. It's three things: a pointer, a length, and a capacity.

```go
// Creating slices
var s []int               // nil slice, len 0
s = []int{1, 2, 3}        // literal
s = make([]int, 5)        // len 5, all zeros: [0 0 0 0 0]
s = make([]int, 0, 10)    // len 0, cap 10 (pre-allocated — efficient)

len(s)  // current length
cap(s)  // capacity before a reallocation is needed

// Appending (returns a NEW slice header — always reassign)
s = append(s, 4)
s = append(s, 5, 6, 7)
s = append(s, other...)   // spread another slice

// Slicing:  s[low:high]  -> elements low .. high-1
nums := []int{0, 1, 2, 3, 4}
nums[1:3]   // [1 2]
nums[:2]    // [0 1]
nums[2:]    // [2 3 4]
nums[:]     // whole slice

// 2D slice (e.g. a grid / DP table)
grid := make([][]int, rows)
for i := range grid {
    grid[i] = make([]int, cols) // each row must be allocated
}
```

**Slice gotchas that bite everyone:**

```go
// 1. Slices share backing arrays — mutation leaks
a := []int{1, 2, 3, 4}
b := a[1:3]
b[0] = 99       // a is now [1 99 3 4]  !!

// 2. To copy, use copy() or full append
c := make([]int, len(a))
copy(c, a)

// 3. Remove element at index i (order not preserved — O(1)):
s[i] = s[len(s)-1]
s = s[:len(s)-1]

// 4. Remove element at index i (order preserved — O(n)):
s = append(s[:i], s[i+1:]...)

// 5. Reverse in place:
for i, j := 0, len(s)-1; i < j; i, j = i+1, j-1 {
    s[i], s[j] = s[j], s[i]
}
```

> **Mental model:** `append` may or may not allocate a new array. Always write `s = append(s, x)`. Never trust that two slices are independent unless you `copy`.

### 2.2 Maps — the hash table

```go
// Creating maps
m := make(map[string]int)
m := map[string]int{"a": 1, "b": 2}   // literal

m["a"] = 10            // set
v := m["a"]            // get (returns zero value if missing!)
v, ok := m["x"]        // comma-ok: ok is false if key absent
delete(m, "a")         // remove
len(m)                 // size

// Iterate (ORDER IS RANDOM — never rely on it)
for k, v := range m {
    fmt.Println(k, v)
}
```

**The "set" idiom** (Go has no built-in set — use a map with empty struct values, which take 0 bytes):

```go
set := make(map[int]struct{})
set[5] = struct{}{}              // add
_, exists := set[5]              // contains
delete(set, 5)                   // remove

// Simpler & more readable for interviews: map[int]bool
seen := make(map[int]bool)
seen[5] = true
if seen[5] { /* ... */ }
```

**Frequency counter** — the single most useful interview idiom:
```go
freq := make(map[byte]int)
for i := 0; i < len(s); i++ {
    freq[s[i]]++   // missing keys default to 0, so this just works
}
```

### 2.3 Structs

```go
type Point struct {
    X, Y int
}

p := Point{1, 2}            // positional
p := Point{X: 1, Y: 2}      // named (preferred)
p := Point{}               // zero value: {0 0}
pp := &Point{1, 2}         // pointer to struct

p.X = 5                    // access (works through pointers too — auto-deref)

// Methods
func (p Point) Dist() float64 {            // value receiver (read-only copy)
    return math.Sqrt(float64(p.X*p.X + p.Y*p.Y))
}
func (p *Point) Scale(f int) {             // pointer receiver (mutates)
    p.X *= f
    p.Y *= f
}
```

> **Rule of thumb:** use a pointer receiver `*T` when the method modifies the struct or the struct is large. Be consistent.

### 2.4 Interfaces (lightly — rarely needed for LeetCode)

An interface is a set of method signatures. Any type implementing them satisfies it implicitly (no `implements` keyword).

```go
type Shape interface {
    Area() float64
}
// If Circle has an Area() float64 method, it IS a Shape automatically.

// The empty interface holds any value (pre-generics):
var anything interface{} = 42   // or `any` in modern Go
```

### 2.5 Generics (Go 1.18+) — handy for reusable helpers

```go
func Max[T int | float64 | string](a, b T) T {
    if a > b {
        return a
    }
    return b
}
Max(3, 7)          // 7
Max("a", "b")      // "b"

// constraints.Ordered from golang.org/x/exp covers all ordered types
```

> You'll rarely *need* generics in an interview, but a small `min`/`max` helper is fine. **Go 1.21+ has built-in `min(a, b)` and `max(a, b)`** for ordered types — use them.

### 2.6 Sorting

```go
import "sort"

nums := []int{3, 1, 2}
sort.Ints(nums)                    // ascending: [1 2 3]
sort.Strings(strs)
sort.Float64s(floats)

// Custom / descending sort:
sort.Slice(nums, func(i, j int) bool {
    return nums[i] > nums[j]        // descending
})

// Sort structs by a field:
sort.Slice(people, func(i, j int) bool {
    return people[i].Age < people[j].Age
})

// Binary search on a sorted slice:
idx := sort.SearchInts(nums, 5)    // leftmost index where 5 could be inserted
```

### 2.7 The `container/heap` package (priority queue)

Go's heap is verbose — you implement an interface. Memorize this template; you'll reuse it for Dijkstra, top-K, merge-K-lists, etc.

```go
import "container/heap"

// A min-heap of ints.
type IntHeap []int

func (h IntHeap) Len() int            { return len(h) }
func (h IntHeap) Less(i, j int) bool  { return h[i] < h[j] } // < = min-heap; > = max-heap
func (h IntHeap) Swap(i, j int)       { h[i], h[j] = h[j], h[i] }
func (h *IntHeap) Push(x any)         { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() any {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

// Usage:
h := &IntHeap{5, 2, 8}
heap.Init(h)            // O(n)
heap.Push(h, 1)         // O(log n)
smallest := (*h)[0]     // peek — O(1)
top := heap.Pop(h).(int) // O(log n), remove smallest
```

For heaps of structs, replace `int` with your struct type in `Push`/`Pop` and `Less` compares the field you care about.

### 2.8 `container/list` (doubly linked list)

```go
import "container/list"
l := list.New()
e := l.PushBack(1)
l.PushFront(0)
l.Remove(e)
for e := l.Front(); e != nil; e = e.Next() {
    v := e.Value
}
```
> Useful for LRU cache, but most tree/list problems use your own struct (see Part 3).

### 2.9 Useful math

```go
import "math"

math.MaxInt32, math.MinInt32
math.MaxInt64, math.MinInt64
math.MaxInt, math.MinInt        // Go 1.17+
math.Abs(x)                     // float64 only — for ints write your own:

func abs(x int) int { if x < 0 { return -x }; return x }
```

---

## Part 3 — Data Structures in Go

These are the building blocks. LeetCode hands you `ListNode` and `TreeNode` definitions for free — but you must know how to traverse and build them.

### 3.1 Dynamic Array → use a slice
Already covered. A Go slice *is* a dynamic array. Append is amortized O(1).

### 3.2 Stack → use a slice

```go
stack := []int{}

stack = append(stack, x)          // push — O(1)
top := stack[len(stack)-1]        // peek — O(1)
stack = stack[:len(stack)-1]      // pop  — O(1)
isEmpty := len(stack) == 0
```

### 3.3 Queue → use a slice (or a real circular buffer)

```go
queue := []int{}
queue = append(queue, x)          // enqueue — O(1)
front := queue[0]                 // peek
queue = queue[1:]                 // dequeue — O(1) amortized, but leaks memory

// For BFS this is fine. For huge queues, use an index pointer:
queue := []int{1, 2, 3}
head := 0
for head < len(queue) {
    cur := queue[head]
    head++
    // queue = append(queue, neighbor)
}
```

### 3.4 Deque → slice with operations on both ends
Push/pop front is O(n) on a slice (shifting). For interviews a slice deque is usually fine; for the sliding-window-maximum pattern we store indices and it stays efficient because each index is added/removed once.

### 3.5 Singly Linked List

```go
type ListNode struct {
    Val  int
    Next *ListNode
}

// Build from a slice
func build(vals []int) *ListNode {
    dummy := &ListNode{}
    cur := dummy
    for _, v := range vals {
        cur.Next = &ListNode{Val: v}
        cur = cur.Next
    }
    return dummy.Next
}

// Traverse
for cur := head; cur != nil; cur = cur.Next {
    fmt.Println(cur.Val)
}
```

**The dummy-node trick** — use a fake head so you never special-case the real head:
```go
dummy := &ListNode{Next: head}
// ... manipulate ...
return dummy.Next
```

**Reverse a linked list** (memorize — it's everywhere):
```go
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode
    cur := head
    for cur != nil {
        next := cur.Next
        cur.Next = prev
        prev = cur
        cur = next
    }
    return prev
}
```

**Fast & slow pointers** (find middle / detect cycle):
```go
slow, fast := head, head
for fast != nil && fast.Next != nil {
    slow = slow.Next
    fast = fast.Next.Next
}
// slow is now the middle. If slow==fast ever, there's a cycle.
```

### 3.6 Binary Tree

```go
type TreeNode struct {
    Val   int
    Left  *TreeNode
    Right *TreeNode
}
```

**The four traversals** — know these in your sleep:

```go
// Recursive DFS
func inorder(root *TreeNode, out *[]int) {
    if root == nil {
        return
    }
    inorder(root.Left, out)     // Left
    *out = append(*out, root.Val) // Node  (pre=Node first, post=Node last)
    inorder(root.Right, out)    // Right
}

// Iterative inorder with an explicit stack
func inorderIter(root *TreeNode) []int {
    var out []int
    stack := []*TreeNode{}
    cur := root
    for cur != nil || len(stack) > 0 {
        for cur != nil {
            stack = append(stack, cur)
            cur = cur.Left
        }
        cur = stack[len(stack)-1]
        stack = stack[:len(stack)-1]
        out = append(out, cur.Val)
        cur = cur.Right
    }
    return out
}

// Level-order (BFS) — uses a queue
func levelOrder(root *TreeNode) [][]int {
    if root == nil {
        return nil
    }
    var res [][]int
    queue := []*TreeNode{root}
    for len(queue) > 0 {
        size := len(queue)
        level := make([]int, 0, size)
        for i := 0; i < size; i++ {
            node := queue[0]
            queue = queue[1:]
            level = append(level, node.Val)
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
        res = append(res, level)
    }
    return res
}
```

> **Binary Search Tree (BST)** property: left subtree < node < right subtree. An **inorder traversal of a BST yields sorted values** — this single fact solves a dozen LeetCode problems.

### 3.7 Heap / Priority Queue
Use `container/heap` (template in Part 2.7). Conceptually: a min-heap gives you the smallest element in O(1) peek, O(log n) push/pop. Used for top-K, Dijkstra, merge-K, median-of-stream.

### 3.8 Graph

Two common representations:

```go
// Adjacency list (most common) — map or slice of slices
graph := map[int][]int{}
graph[0] = []int{1, 2}
graph[1] = []int{2}

// For nodes 0..n-1:
adj := make([][]int, n)
adj[u] = append(adj[u], v)   // edge u->v
adj[v] = append(adj[v], u)   // add this line too for UNDIRECTED graphs
```

**Graph DFS:**
```go
func dfs(node int, adj [][]int, visited []bool) {
    visited[node] = true
    for _, next := range adj[node] {
        if !visited[next] {
            dfs(next, adj, visited)
        }
    }
}
```

**Graph BFS:**
```go
func bfs(start int, adj [][]int) {
    visited := make([]bool, len(adj))
    queue := []int{start}
    visited[start] = true
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        for _, next := range adj[node] {
            if !visited[next] {
                visited[next] = true
                queue = append(queue, next)
            }
        }
    }
}
```

> For **grid problems** (islands, mazes), the "graph" is the 2D matrix; neighbors are up/down/left/right. Use a `dirs := [][]int{{0,1},{1,0},{0,-1},{-1,0}}` helper to loop over the 4 directions.

### 3.9 Union-Find (Disjoint Set Union)

Essential for connectivity / grouping problems. With path compression + union by rank it's near-O(1) per op.

```go
type DSU struct {
    parent []int
    rank   []int
}

func NewDSU(n int) *DSU {
    d := &DSU{parent: make([]int, n), rank: make([]int, n)}
    for i := range d.parent {
        d.parent[i] = i // each node is its own parent initially
    }
    return d
}

func (d *DSU) Find(x int) int {
    if d.parent[x] != x {
        d.parent[x] = d.Find(d.parent[x]) // path compression
    }
    return d.parent[x]
}

func (d *DSU) Union(x, y int) bool {
    rx, ry := d.Find(x), d.Find(y)
    if rx == ry {
        return false // already connected
    }
    if d.rank[rx] < d.rank[ry] {
        rx, ry = ry, rx
    }
    d.parent[ry] = rx
    if d.rank[rx] == d.rank[ry] {
        d.rank[rx]++
    }
    return true
}
```

### 3.10 Trie (Prefix Tree)

For word/prefix lookups (autocomplete, word search II).

```go
type Trie struct {
    children [26]*Trie
    isEnd    bool
}

func Constructor() Trie {
    return Trie{}
}

func (t *Trie) Insert(word string) {
    node := t
    for i := 0; i < len(word); i++ {
        c := word[i] - 'a'
        if node.children[c] == nil {
            node.children[c] = &Trie{}
        }
        node = node.children[c]
    }
    node.isEnd = true
}

func (t *Trie) find(word string) *Trie {
    node := t
    for i := 0; i < len(word); i++ {
        c := word[i] - 'a'
        if node.children[c] == nil {
            return nil
        }
        node = node.children[c]
    }
    return node
}

func (t *Trie) Search(word string) bool {
    n := t.find(word)
    return n != nil && n.isEnd
}

func (t *Trie) StartsWith(prefix string) bool {
    return t.find(prefix) != nil
}
```

---

## Part 4 — The 15 Patterns + LeetCode Problems

> **How to use this part:** For each pattern, read *when to reach for it*, internalize the template, then study the worked solutions. The skill you're building is **pattern recognition** — mapping a new problem to one of these in under a minute.

Difficulty key: 🟢 Easy · 🟡 Medium · 🔴 Hard

---

### Pattern 1 — Two Pointers

**Reach for it when:** the input is sorted (or can be), and you're looking for a pair/triplet, or comparing from both ends, or partitioning in place.

**Template:**
```go
left, right := 0, len(arr)-1
for left < right {
    if /* condition met */ {
        // record answer, move both
        left++; right--
    } else if /* need bigger sum */ {
        left++
    } else {
        right--
    }
}
```

#### 🟢 Two Sum II — Input Array Is Sorted (LC 167)
```go
func twoSum(numbers []int, target int) []int {
    l, r := 0, len(numbers)-1
    for l < r {
        sum := numbers[l] + numbers[r]
        switch {
        case sum == target:
            return []int{l + 1, r + 1} // 1-indexed
        case sum < target:
            l++
        default:
            r--
        }
    }
    return nil
}
```

#### 🟡 3Sum (LC 15)
Sort, fix one number, two-pointer the rest. The key extra work is skipping duplicates.
```go
func threeSum(nums []int) [][]int {
    sort.Ints(nums)
    var res [][]int
    for i := 0; i < len(nums)-2; i++ {
        if i > 0 && nums[i] == nums[i-1] {
            continue // skip duplicate anchor
        }
        l, r := i+1, len(nums)-1
        for l < r {
            sum := nums[i] + nums[l] + nums[r]
            switch {
            case sum == 0:
                res = append(res, []int{nums[i], nums[l], nums[r]})
                l++
                r--
                for l < r && nums[l] == nums[l-1] { l++ } // skip dup
                for l < r && nums[r] == nums[r+1] { r-- } // skip dup
            case sum < 0:
                l++
            default:
                r--
            }
        }
    }
    return res
}
```

#### 🔴 Trapping Rain Water (LC 42)
```go
func trap(height []int) int {
    l, r := 0, len(height)-1
    leftMax, rightMax, water := 0, 0, 0
    for l < r {
        if height[l] < height[r] {
            if height[l] >= leftMax {
                leftMax = height[l]
            } else {
                water += leftMax - height[l]
            }
            l++
        } else {
            if height[r] >= rightMax {
                rightMax = height[r]
            } else {
                water += rightMax - height[r]
            }
            r--
        }
    }
    return water
}
```

**More to practice:** Valid Palindrome (125), Container With Most Water (11), Remove Duplicates from Sorted Array (26), Sort Colors (75).

---

### Pattern 2 — Sliding Window

**Reach for it when:** you want the longest/shortest/best **contiguous** subarray or substring satisfying a constraint. Turns an O(n²) brute force into O(n).

**Template (variable-size window):**
```go
left := 0
for right := 0; right < len(s); right++ {
    // 1. expand: include s[right] in the window
    for /* window is invalid */ {
        // 2. shrink: remove s[left], left++
        left++
    }
    // 3. window [left..right] is now valid — update the answer
}
```

#### 🟡 Longest Substring Without Repeating Characters (LC 3)
```go
func lengthOfLongestSubstring(s string) int {
    last := make(map[byte]int) // char -> last index seen
    left, best := 0, 0
    for right := 0; right < len(s); right++ {
        c := s[right]
        if idx, ok := last[c]; ok && idx >= left {
            left = idx + 1 // jump past the duplicate
        }
        last[c] = right
        if right-left+1 > best {
            best = right - left + 1
        }
    }
    return best
}
```

#### 🟡 Minimum Window Substring (LC 76) — the hard-mode template
```go
func minWindow(s, t string) string {
    need := make(map[byte]int)
    for i := 0; i < len(t); i++ {
        need[t[i]]++
    }
    missing := len(t)
    left, start, minLen := 0, 0, math.MaxInt32
    for right := 0; right < len(s); right++ {
        if need[s[right]] > 0 {
            missing--
        }
        need[s[right]]--
        for missing == 0 { // valid window — shrink it
            if right-left+1 < minLen {
                minLen = right - left + 1
                start = left
            }
            need[s[left]]++
            if need[s[left]] > 0 {
                missing++
            }
            left++
        }
    }
    if minLen == math.MaxInt32 {
        return ""
    }
    return s[start : start+minLen]
}
```

#### 🟡 Sliding Window Maximum (LC 239) — monotonic deque
```go
func maxSlidingWindow(nums []int, k int) []int {
    var res []int
    deque := []int{} // stores INDICES, values decreasing
    for i, n := range nums {
        // drop smaller values from the back
        for len(deque) > 0 && nums[deque[len(deque)-1]] < n {
            deque = deque[:len(deque)-1]
        }
        deque = append(deque, i)
        // drop the front if it's outside the window
        if deque[0] <= i-k {
            deque = deque[1:]
        }
        if i >= k-1 {
            res = append(res, nums[deque[0]])
        }
    }
    return res
}
```

**More to practice:** Best Time to Buy/Sell Stock (121), Longest Repeating Character Replacement (424), Permutation in String (567), Fruit Into Baskets (904).

---

### Pattern 3 — Hashing & Frequency Maps

**Reach for it when:** you need O(1) lookups, grouping, counting, or "have I seen this before?". The most-used pattern overall.

#### 🟢 Two Sum (LC 1) — the canonical hashing problem
```go
func twoSum(nums []int, target int) []int {
    seen := make(map[int]int) // value -> index
    for i, n := range nums {
        if j, ok := seen[target-n]; ok {
            return []int{j, i}
        }
        seen[n] = i
    }
    return nil
}
```

#### 🟡 Group Anagrams (LC 49)
```go
func groupAnagrams(strs []string) [][]string {
    groups := make(map[[26]int][]string)
    for _, s := range strs {
        var key [26]int // arrays are comparable, so usable as a map key
        for i := 0; i < len(s); i++ {
            key[s[i]-'a']++
        }
        groups[key] = append(groups[key], s)
    }
    res := make([][]string, 0, len(groups))
    for _, g := range groups {
        res = append(res, g)
    }
    return res
}
```

#### 🟡 Longest Consecutive Sequence (LC 128) — O(n) with a set
```go
func longestConsecutive(nums []int) int {
    set := make(map[int]bool)
    for _, n := range nums {
        set[n] = true
    }
    best := 0
    for n := range set {
        if set[n-1] {
            continue // not the start of a sequence
        }
        length := 1
        for set[n+length] {
            length++
        }
        if length > best {
            best = length
        }
    }
    return best
}
```

**More to practice:** Contains Duplicate (217), Valid Anagram (242), Top K Frequent Elements (347), Subarray Sum Equals K (560).

---

### Pattern 4 — Binary Search

**Reach for it when:** the search space is sorted OR monotonic ("is X achievable?" gets harder/easier as X grows). O(log n).

**Template (and avoid the off-by-one traps):**
```go
lo, hi := 0, len(arr)-1
for lo <= hi {
    mid := lo + (hi-lo)/2  // avoids overflow
    switch {
    case arr[mid] == target:
        return mid
    case arr[mid] < target:
        lo = mid + 1
    default:
        hi = mid - 1
    }
}
return -1 // not found
```

#### 🟡 Search in Rotated Sorted Array (LC 33)
```go
func search(nums []int, target int) int {
    lo, hi := 0, len(nums)-1
    for lo <= hi {
        mid := lo + (hi-lo)/2
        if nums[mid] == target {
            return mid
        }
        if nums[lo] <= nums[mid] { // left half is sorted
            if nums[lo] <= target && target < nums[mid] {
                hi = mid - 1
            } else {
                lo = mid + 1
            }
        } else { // right half is sorted
            if nums[mid] < target && target <= nums[hi] {
                lo = mid + 1
            } else {
                hi = mid - 1
            }
        }
    }
    return -1
}
```

#### 🟡 Find Minimum in Rotated Sorted Array (LC 153)
```go
func findMin(nums []int) int {
    lo, hi := 0, len(nums)-1
    for lo < hi {
        mid := lo + (hi-lo)/2
        if nums[mid] > nums[hi] {
            lo = mid + 1 // min is to the right
        } else {
            hi = mid     // min is at mid or to the left
        }
    }
    return nums[lo]
}
```

#### 🟡 Koko Eating Bananas (LC 875) — "binary search on the answer"
This is the pattern Google loves: you binary-search over *possible answers*, not array indices.
```go
func minEatingSpeed(piles []int, h int) int {
    canFinish := func(speed int) bool {
        hours := 0
        for _, p := range piles {
            hours += (p + speed - 1) / speed // ceil division
        }
        return hours <= h
    }
    lo, hi := 1, 0
    for _, p := range piles {
        if p > hi {
            hi = p
        }
    }
    for lo < hi {
        mid := lo + (hi-lo)/2
        if canFinish(mid) {
            hi = mid
        } else {
            lo = mid + 1
        }
    }
    return lo
}
```

**More to practice:** Binary Search (704), Search a 2D Matrix (74), Find First and Last Position (34), Median of Two Sorted Arrays (4, 🔴).

---

### Pattern 5 — Stack (including Monotonic Stack)

**Reach for it when:** matching pairs (parentheses), "next greater/smaller element," undo/evaluation, or anything LIFO.

#### 🟢 Valid Parentheses (LC 20)
```go
func isValid(s string) bool {
    pairs := map[byte]byte{')': '(', ']': '[', '}': '{'}
    stack := []byte{}
    for i := 0; i < len(s); i++ {
        c := s[i]
        if open, isClose := pairs[c]; isClose {
            if len(stack) == 0 || stack[len(stack)-1] != open {
                return false
            }
            stack = stack[:len(stack)-1]
        } else {
            stack = append(stack, c)
        }
    }
    return len(stack) == 0
}
```

#### 🟡 Daily Temperatures (LC 739) — monotonic stack
```go
func dailyTemperatures(temps []int) []int {
    res := make([]int, len(temps))
    stack := []int{} // indices, temps decreasing
    for i, t := range temps {
        for len(stack) > 0 && temps[stack[len(stack)-1]] < t {
            j := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            res[j] = i - j
        }
        stack = append(stack, i)
    }
    return res
}
```

#### 🔴 Largest Rectangle in Histogram (LC 84)
```go
func largestRectangleArea(heights []int) int {
    heights = append(heights, 0) // sentinel flushes the stack at the end
    stack := []int{}             // indices, heights increasing
    best := 0
    for i, h := range heights {
        for len(stack) > 0 && heights[stack[len(stack)-1]] > h {
            top := stack[len(stack)-1]
            stack = stack[:len(stack)-1]
            width := i
            if len(stack) > 0 {
                width = i - stack[len(stack)-1] - 1
            }
            if area := heights[top] * width; area > best {
                best = area
            }
        }
        stack = append(stack, i)
    }
    return best
}
```

**More to practice:** Min Stack (155), Evaluate Reverse Polish Notation (150), Next Greater Element (496), Car Fleet (853).

---

### Pattern 6 — Linked List Manipulation

**Reach for it when:** the input is a linked list. Master the **dummy node** and **fast/slow pointer** tricks (Part 3.5) and most of these collapse.

#### 🟢 Reverse Linked List (LC 206) — see Part 3.5. Memorize it.

#### 🟢 Merge Two Sorted Lists (LC 21)
```go
func mergeTwoLists(l1, l2 *ListNode) *ListNode {
    dummy := &ListNode{}
    tail := dummy
    for l1 != nil && l2 != nil {
        if l1.Val <= l2.Val {
            tail.Next = l1
            l1 = l1.Next
        } else {
            tail.Next = l2
            l2 = l2.Next
        }
        tail = tail.Next
    }
    if l1 != nil {
        tail.Next = l1
    } else {
        tail.Next = l2
    }
    return dummy.Next
}
```

#### 🟢 Linked List Cycle (LC 141) — Floyd's algorithm
```go
func hasCycle(head *ListNode) bool {
    slow, fast := head, head
    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next
        if slow == fast {
            return true
        }
    }
    return false
}
```

#### 🟡 Remove Nth Node From End (LC 19) — two pointers, gap of n
```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    dummy := &ListNode{Next: head}
    fast, slow := dummy, dummy
    for i := 0; i < n; i++ {
        fast = fast.Next
    }
    for fast.Next != nil {
        fast = fast.Next
        slow = slow.Next
    }
    slow.Next = slow.Next.Next
    return dummy.Next
}
```

**More to practice:** Reorder List (143), Add Two Numbers (2), Copy List with Random Pointer (138), LRU Cache (146).

---

### Pattern 7 — Trees (DFS & BFS)

**Reach for it when:** the input is a tree. Decide: does the answer come from a **traversal** (BFS for levels, DFS for paths) or from **divide-and-conquer** (compute on children, combine at the node)?

**The divide-and-conquer template** — the mental model for most tree problems:
```go
func solve(node *TreeNode) ResultType {
    if node == nil {
        return baseCase
    }
    left := solve(node.Left)
    right := solve(node.Right)
    return combine(node, left, right)
}
```

#### 🟢 Maximum Depth (LC 104)
```go
func maxDepth(root *TreeNode) int {
    if root == nil {
        return 0
    }
    l, r := maxDepth(root.Left), maxDepth(root.Right)
    if l > r {
        return l + 1
    }
    return r + 1
}
```

#### 🟢 Invert Binary Tree (LC 226)
```go
func invertTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    root.Left, root.Right = invertTree(root.Right), invertTree(root.Left)
    return root
}
```

#### 🟡 Validate BST (LC 98) — pass down allowed (min, max) bounds
```go
func isValidBST(root *TreeNode) bool {
    var check func(n *TreeNode, lo, hi int) bool
    check = func(n *TreeNode, lo, hi int) bool {
        if n == nil {
            return true
        }
        if n.Val <= lo || n.Val >= hi {
            return false
        }
        return check(n.Left, lo, n.Val) && check(n.Right, n.Val, hi)
    }
    return check(root, math.MinInt, math.MaxInt)
}
```

#### 🟡 Lowest Common Ancestor (LC 236)
```go
func lowestCommonAncestor(root, p, q *TreeNode) *TreeNode {
    if root == nil || root == p || root == q {
        return root
    }
    left := lowestCommonAncestor(root.Left, p, q)
    right := lowestCommonAncestor(root.Right, p, q)
    if left != nil && right != nil {
        return root // p and q split here
    }
    if left != nil {
        return left
    }
    return right
}
```

#### 🔴 Binary Tree Maximum Path Sum (LC 124) — classic "combine at node"
```go
func maxPathSum(root *TreeNode) int {
    best := math.MinInt
    var gain func(n *TreeNode) int
    gain = func(n *TreeNode) int {
        if n == nil {
            return 0
        }
        l := max(0, gain(n.Left))  // ignore negative paths
        r := max(0, gain(n.Right))
        best = max(best, n.Val+l+r) // path THROUGH n
        return n.Val + max(l, r)    // best single branch to extend upward
    }
    gain(root)
    return best
}
// (Go 1.21+ provides built-in max; otherwise define your own.)
```

**More to practice:** Same Tree (100), Diameter of Binary Tree (543), Level Order (102), Serialize/Deserialize (297, 🔴), Kth Smallest in BST (230).

---

### Pattern 8 — Heaps & Top-K

**Reach for it when:** you need the K largest/smallest, a running median, or to repeatedly pull the min/max. Use `container/heap` (Part 2.7).

#### 🟡 Kth Largest Element in Array (LC 215)
A min-heap of size k: the root is always the kth largest.
```go
func findKthLargest(nums []int, k int) int {
    h := &IntHeap{} // min-heap from Part 2.7
    heap.Init(h)
    for _, n := range nums {
        heap.Push(h, n)
        if h.Len() > k {
            heap.Pop(h) // discard smallest, keep k largest
        }
    }
    return (*h)[0]
}
```
> In an interview, mention that **Quickselect** gives expected O(n) vs the heap's O(n log k). Heap is simpler to code correctly under pressure.

#### 🟡 Top K Frequent Elements (LC 347) — bucket sort, O(n)
```go
func topKFrequent(nums []int, k int) []int {
    freq := make(map[int]int)
    for _, n := range nums {
        freq[n]++
    }
    buckets := make([][]int, len(nums)+1) // index = frequency
    for n, f := range freq {
        buckets[f] = append(buckets[f], n)
    }
    var res []int
    for f := len(buckets) - 1; f >= 0 && len(res) < k; f-- {
        res = append(res, buckets[f]...)
    }
    return res[:k]
}
```

#### 🔴 Find Median from Data Stream (LC 295) — two heaps
Keep a max-heap of the lower half and a min-heap of the upper half, balanced in size.

**More to practice:** K Closest Points to Origin (973), Merge k Sorted Lists (23, 🔴), Task Scheduler (621).

---

### Pattern 9 — Backtracking

**Reach for it when:** you must generate **all** combinations/permutations/subsets, or explore a decision tree (N-Queens, Sudoku, word search). Exponential by nature.

**The universal template:**
```go
func backtrack(path []T, choices ...) {
    if /* goal reached */ {
        // record a COPY of path (slices are shared!)
        result = append(result, append([]T{}, path...))
        return
    }
    for _, choice := range available {
        path = append(path, choice)   // choose
        backtrack(path, ...)          // explore
        path = path[:len(path)-1]     // un-choose (backtrack)
    }
}
```

#### 🟡 Subsets (LC 78)
```go
func subsets(nums []int) [][]int {
    var res [][]int
    var path []int
    var bt func(start int)
    bt = func(start int) {
        res = append(res, append([]int{}, path...)) // copy current subset
        for i := start; i < len(nums); i++ {
            path = append(path, nums[i])
            bt(i + 1)
            path = path[:len(path)-1]
        }
    }
    bt(0)
    return res
}
```

#### 🟡 Permutations (LC 46)
```go
func permute(nums []int) [][]int {
    var res [][]int
    used := make([]bool, len(nums))
    var path []int
    var bt func()
    bt = func() {
        if len(path) == len(nums) {
            res = append(res, append([]int{}, path...))
            return
        }
        for i := range nums {
            if used[i] {
                continue
            }
            used[i] = true
            path = append(path, nums[i])
            bt()
            path = path[:len(path)-1]
            used[i] = false
        }
    }
    bt()
    return res
}
```

#### 🟡 Combination Sum (LC 39) — reuse allowed
```go
func combinationSum(candidates []int, target int) [][]int {
    var res [][]int
    var path []int
    var bt func(start, remain int)
    bt = func(start, remain int) {
        if remain == 0 {
            res = append(res, append([]int{}, path...))
            return
        }
        for i := start; i < len(candidates); i++ {
            if candidates[i] > remain {
                continue
            }
            path = append(path, candidates[i])
            bt(i, remain-candidates[i]) // i (not i+1) → reuse same number
            path = path[:len(path)-1]
        }
    }
    bt(0, target)
    return res
}
```

**More to practice:** Word Search (79), Combinations (77), Generate Parentheses (22), N-Queens (51, 🔴), Palindrome Partitioning (131).

---

### Pattern 10 — Graphs (BFS, DFS, Topological Sort)

**Reach for it when:** explicit graphs, grids, dependency ordering, or shortest path in an unweighted graph (BFS).

#### 🟡 Number of Islands (LC 200) — grid DFS
```go
func numIslands(grid [][]byte) int {
    rows, cols := len(grid), len(grid[0])
    var sink func(r, c int)
    sink = func(r, c int) {
        if r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] != '1' {
            return
        }
        grid[r][c] = '0' // mark visited
        sink(r+1, c)
        sink(r-1, c)
        sink(r, c+1)
        sink(r, c-1)
    }
    count := 0
    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if grid[r][c] == '1' {
                count++
                sink(r, c)
            }
        }
    }
    return count
}
```

#### 🟡 Course Schedule (LC 207) — cycle detection / topological sort (Kahn's BFS)
```go
func canFinish(numCourses int, prerequisites [][]int) bool {
    adj := make([][]int, numCourses)
    indegree := make([]int, numCourses)
    for _, p := range prerequisites {
        adj[p[1]] = append(adj[p[1]], p[0]) // p[1] -> p[0]
        indegree[p[0]]++
    }
    queue := []int{}
    for i := 0; i < numCourses; i++ {
        if indegree[i] == 0 {
            queue = append(queue, i)
        }
    }
    done := 0
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:]
        done++
        for _, next := range adj[node] {
            indegree[next]--
            if indegree[next] == 0 {
                queue = append(queue, next)
            }
        }
    }
    return done == numCourses // if not all processed, there's a cycle
}
```

#### 🟡 Rotting Oranges (LC 994) — multi-source BFS
```go
func orangesRotting(grid [][]int) int {
    rows, cols := len(grid), len(grid[0])
    queue := [][2]int{}
    fresh := 0
    for r := 0; r < rows; r++ {
        for c := 0; c < cols; c++ {
            if grid[r][c] == 2 {
                queue = append(queue, [2]int{r, c})
            } else if grid[r][c] == 1 {
                fresh++
            }
        }
    }
    dirs := [][2]int{{0, 1}, {0, -1}, {1, 0}, {-1, 0}}
    minutes := 0
    for len(queue) > 0 && fresh > 0 {
        minutes++
        for size := len(queue); size > 0; size-- {
            cell := queue[0]
            queue = queue[1:]
            for _, d := range dirs {
                nr, nc := cell[0]+d[0], cell[1]+d[1]
                if nr >= 0 && nc >= 0 && nr < rows && nc < cols && grid[nr][nc] == 1 {
                    grid[nr][nc] = 2
                    fresh--
                    queue = append(queue, [2]int{nr, nc})
                }
            }
        }
    }
    if fresh > 0 {
        return -1
    }
    return minutes
}
```

> **Dijkstra** (weighted shortest path) = BFS + a min-heap, popping the cheapest node each step. Practice it on Network Delay Time (743) and Cheapest Flights (787).

**More to practice:** Clone Graph (133), Pacific Atlantic Water Flow (417), Word Ladder (127, 🔴), Number of Connected Components (323 — use Union-Find), Alien Dictionary (269, 🔴).

---

### Pattern 11 — Dynamic Programming (DP)

**Reach for it when:** the problem asks for an optimal value (max/min/count/true-false), the answer to a problem depends on answers to **smaller overlapping subproblems**, and greedy doesn't work.

**The DP recipe (use this exact process out loud in interviews):**
1. **Define the state.** What does `dp[i]` (or `dp[i][j]`) *mean* in plain English?
2. **Find the recurrence.** How is `dp[i]` built from earlier states?
3. **Base cases.** What are the smallest states?
4. **Order of computation.** Iterate so dependencies are ready.
5. **Answer location.** Which cell holds the final answer?
6. **(Optional) Space optimization.** Often only the last row/few values are needed.

Start with **top-down memoization** (recursion + cache) because it mirrors the recurrence directly, then convert to **bottom-up** if needed.

#### 🟢 Climbing Stairs (LC 70) — the "Hello World" of DP
`dp[i]` = ways to reach step i. Recurrence: `dp[i] = dp[i-1] + dp[i-2]` (Fibonacci).
```go
func climbStairs(n int) int {
    if n <= 2 {
        return n
    }
    prev2, prev1 := 1, 2
    for i := 3; i <= n; i++ {
        prev2, prev1 = prev1, prev1+prev2
    }
    return prev1
}
```

#### 🟡 House Robber (LC 198) — can't rob adjacent houses
`dp[i]` = max money robbing houses 0..i. `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`.
```go
func rob(nums []int) int {
    prev2, prev1 := 0, 0
    for _, n := range nums {
        prev2, prev1 = prev1, max(prev1, prev2+n)
    }
    return prev1
}
```

#### 🟡 Coin Change (LC 322) — unbounded knapsack, min coins
`dp[a]` = fewest coins to make amount a.
```go
func coinChange(coins []int, amount int) int {
    const INF = 1 << 30
    dp := make([]int, amount+1)
    for i := 1; i <= amount; i++ {
        dp[i] = INF
    }
    for a := 1; a <= amount; a++ {
        for _, c := range coins {
            if c <= a && dp[a-c]+1 < dp[a] {
                dp[a] = dp[a-c] + 1
            }
        }
    }
    if dp[amount] >= INF {
        return -1
    }
    return dp[amount]
}
```

#### 🟡 Longest Increasing Subsequence (LC 300)
`dp[i]` = length of LIS ending at index i. O(n²) version (the O(n log n) patience-sorting version is a great follow-up to mention):
```go
func lengthOfLIS(nums []int) int {
    dp := make([]int, len(nums))
    best := 0
    for i := range nums {
        dp[i] = 1
        for j := 0; j < i; j++ {
            if nums[j] < nums[i] && dp[j]+1 > dp[i] {
                dp[i] = dp[j] + 1
            }
        }
        if dp[i] > best {
            best = dp[i]
        }
    }
    return best
}
```

#### 🟡 Longest Common Subsequence (LC 1143) — 2D DP, the grid template
`dp[i][j]` = LCS of `text1[:i]` and `text2[:j]`.
```go
func longestCommonSubsequence(text1, text2 string) int {
    m, n := len(text1), len(text2)
    dp := make([][]int, m+1)
    for i := range dp {
        dp[i] = make([]int, n+1)
    }
    for i := 1; i <= m; i++ {
        for j := 1; j <= n; j++ {
            if text1[i-1] == text2[j-1] {
                dp[i][j] = dp[i-1][j-1] + 1
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
            }
        }
    }
    return dp[m][n]
}
```

#### 🟡 0/1 Knapsack (the canonical template behind many DP problems)
`dp[w]` = max value with capacity w. Iterate weights **backward** so each item is used once.
```go
func knapsack(weights, values []int, capacity int) int {
    dp := make([]int, capacity+1)
    for i := range weights {
        for w := capacity; w >= weights[i]; w-- {
            if dp[w-weights[i]]+values[i] > dp[w] {
                dp[w] = dp[w-weights[i]] + values[i]
            }
        }
    }
    return dp[capacity]
}
```

**DP problem families to drill:**
- *1D:* Climbing Stairs (70), House Robber (198/213), Maximum Subarray (53), Decode Ways (91), Word Break (139).
- *2D grid:* Unique Paths (62), Minimum Path Sum (64), Edit Distance (72, 🔴), LCS (1143).
- *Knapsack:* Coin Change (322), Partition Equal Subset Sum (416), Target Sum (494).
- *Intervals/strings:* Longest Palindromic Substring (5), Palindromic Substrings (647).
- *Hard classics:* Regular Expression Matching (10), Burst Balloons (312), Longest Increasing Path in Matrix (329).

---

### Pattern 12 — Greedy

**Reach for it when:** a locally optimal choice provably leads to a global optimum. Greedy is fast (often O(n) or O(n log n)) but you must **prove** it's correct — interviewers will probe this.

#### 🟡 Jump Game (LC 55) — track the farthest reachable index
```go
func canJump(nums []int) bool {
    reach := 0
    for i, n := range nums {
        if i > reach {
            return false // can't even get here
        }
        if i+n > reach {
            reach = i + n
        }
    }
    return true
}
```

#### 🟢 Maximum Subarray (LC 53) — Kadane's algorithm (greedy/DP hybrid)
```go
func maxSubArray(nums []int) int {
    best, cur := nums[0], nums[0]
    for i := 1; i < len(nums); i++ {
        cur = max(nums[i], cur+nums[i]) // extend or restart
        best = max(best, cur)
    }
    return best
}
```

#### 🟡 Gas Station (LC 134)
```go
func canCompleteCircuit(gas, cost []int) int {
    total, tank, start := 0, 0, 0
    for i := range gas {
        diff := gas[i] - cost[i]
        total += diff
        tank += diff
        if tank < 0 { // can't reach i+1 from current start
            start = i + 1
            tank = 0
        }
    }
    if total < 0 {
        return -1
    }
    return start
}
```

**More to practice:** Jump Game II (45), Partition Labels (763), Hand of Straights (846), Merge Intervals (56, see next).

---

### Pattern 13 — Intervals

**Reach for it when:** the input is a list of `[start, end]` ranges. **Almost always: sort first** (by start or end), then sweep.

#### 🟡 Merge Intervals (LC 56)
```go
func merge(intervals [][]int) [][]int {
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][0] < intervals[j][0]
    })
    res := [][]int{intervals[0]}
    for _, cur := range intervals[1:] {
        last := res[len(res)-1]
        if cur[0] <= last[1] { // overlap
            if cur[1] > last[1] {
                last[1] = cur[1] // extend
            }
        } else {
            res = append(res, cur)
        }
    }
    return res
}
```

#### 🟡 Non-overlapping Intervals (LC 435) — sort by END, greedily keep
```go
func eraseOverlapIntervals(intervals [][]int) int {
    sort.Slice(intervals, func(i, j int) bool {
        return intervals[i][1] < intervals[j][1]
    })
    removed := 0
    prevEnd := math.MinInt
    for _, in := range intervals {
        if in[0] >= prevEnd {
            prevEnd = in[1] // keep it
        } else {
            removed++ // overlaps — remove this one
        }
    }
    return removed
}
```

**More to practice:** Insert Interval (57), Meeting Rooms (252), Meeting Rooms II (253 — min-heap of end times), Minimum Number of Arrows (452).

---

### Pattern 14 — Bit Manipulation

**Reach for it when:** XOR tricks, counting bits, subsets via bitmasks, or O(1)-space "find the unique number" problems.

**The essential operations:**
```go
x & 1            // is x odd? / lowest bit
x >> 1           // divide by 2
x << 1           // multiply by 2
x & (x - 1)      // clears the lowest set bit  (counting bits!)
x & (-x)         // isolates the lowest set bit
x ^ y            // XOR: a^a=0, a^0=a, commutative — the workhorse
x | (1 << i)     // set bit i
x & ^(1 << i)    // clear bit i
(x >> i) & 1     // read bit i
```

#### 🟢 Single Number (LC 136) — XOR cancels pairs
```go
func singleNumber(nums []int) int {
    res := 0
    for _, n := range nums {
        res ^= n // duplicates cancel, the unique one remains
    }
    return res
}
```

#### 🟢 Number of 1 Bits (LC 191)
```go
func hammingWeight(n uint32) int {
    count := 0
    for n != 0 {
        n &= n - 1 // clear lowest set bit each iteration
        count++
    }
    return count
}
```

#### 🟢 Counting Bits (LC 338) — DP on bits
```go
func countBits(n int) []int {
    dp := make([]int, n+1)
    for i := 1; i <= n; i++ {
        dp[i] = dp[i>>1] + (i & 1) // bits in i = bits in i/2, plus last bit
    }
    return dp
}
```

**More to practice:** Missing Number (268), Reverse Bits (190), Sum of Two Integers (371), Subsets via bitmask.

---

### Pattern 15 — Prefix Sums & Math

**Reach for it when:** you need fast range-sum queries, "subarrays summing to K," or there's a clean numeric/arithmetic insight.

#### 🟡 Subarray Sum Equals K (LC 560) — prefix sum + hashmap
```go
func subarraySum(nums []int, k int) int {
    count := 0
    sum := 0
    seen := map[int]int{0: 1} // prefix sum -> how many times seen
    for _, n := range nums {
        sum += n
        if c, ok := seen[sum-k]; ok {
            count += c // a previous prefix makes the gap == k
        }
        seen[sum]++
    }
    return count
}
```

#### 🟡 Product of Array Except Self (LC 238) — prefix & suffix products, no division
```go
func productExceptSelf(nums []int) []int {
    n := len(nums)
    res := make([]int, n)
    res[0] = 1
    for i := 1; i < n; i++ {
        res[i] = res[i-1] * nums[i-1] // product of everything to the LEFT
    }
    right := 1
    for i := n - 1; i >= 0; i-- {
        res[i] *= right            // multiply by product to the RIGHT
        right *= nums[i]
    }
    return res
}
```

**More to practice:** Range Sum Query (303), Pivot Index (724), Range Sum Query 2D (304), Continuous Subarray Sum (523).

---

## Part 5 — Google Interview Playbook

### 5.1 The structure of a 45-minute coding round
1. **Introductions** (~3 min).
2. **The problem is stated**, often deliberately underspecified.
3. **You clarify & explore** (~5 min) — *do not start coding yet.*
4. **You propose an approach & complexity** — get a nod before coding.
5. **You code** (~20 min) while narrating.
6. **You test** with examples and edge cases.
7. **Follow-ups** — they scale the problem up or add a twist.

### 5.2 The framework: U.M.P.I.R.E.
A reliable script to run on every problem:

- **U — Understand.** Restate the problem. Ask about input size, ranges, duplicates, empty/null inputs, sorted-ness, return format. *"Can the array be empty? Are values unique? How large can n get?"*
- **M — Match.** Which of the 15 patterns fits? Sorted → binary search / two pointers. Contiguous subarray → sliding window. "All combinations" → backtracking. Tree → DFS/BFS. Optimal + overlapping subproblems → DP.
- **P — Plan.** Describe the algorithm in plain English / pseudocode *before* typing. State the data structures.
- **I — Implement.** Write clean Go. Narrate as you go. Use good names.
- **R — Review.** Walk through your code with a concrete example, line by line. Catch off-by-ones.
- **E — Evaluate.** State time & space complexity. Discuss trade-offs and how you'd handle scale.

### 5.3 Clarifying questions you should almost always ask
- What are the input size and value ranges? (Tells you the target complexity.)
- Can the input be empty / null / a single element?
- Are there duplicates? Is it sorted?
- Are there negative numbers?
- What should I return if there's no valid answer?
- Should I optimize for time or space?

> **Complexity from constraints (a Google favorite trick):** n ≤ 20 → exponential (backtracking/bitmask) is fine. n ≤ 5,000 → O(n²) is fine. n ≤ 10⁵–10⁶ → you need O(n log n) or O(n). n ≥ 10⁹ → O(log n) or math.

### 5.4 What Google actually scores you on
Google rates each round on four axes:
- **Algorithmic problem solving** — did you find an efficient, correct approach?
- **Coding** — is the code clean, correct, and idiomatic?
- **Communication** — did you think out loud and collaborate?
- **Edge cases / testing** — did you verify correctness yourself?

A working brute force *with* great communication often beats a half-finished "optimal" solution in silence. **Always get something working first, then optimize.**

### 5.5 Behavioral round (the "Googleyness & Leadership" interview)
Prepare 5–6 stories using **STAR** (Situation, Task, Action, Result). Cover: a hard technical problem, a conflict with a teammate, a failure and what you learned, leading/influencing without authority, and ambiguity. Be specific, quantify results, and emphasize collaboration.

### 5.6 Go-specific interview tips
- Use the helper `func abs(x int) int` and `min`/`max` (built-in on Go 1.21+) — don't waste time.
- Prefer `[]byte(s)` over `[]rune(s)` for ASCII string problems (faster, simpler indexing).
- Pre-size slices/maps with `make([]T, 0, n)` when you know the size — shows you understand allocations.
- Remember to **copy** slices when storing them in results (backtracking!) — sharing is the #1 Go bug in interviews.
- `sort.Slice` with a closure handles any custom ordering.
- Mention `container/heap` by name even if you write a quick template — it signals stdlib fluency.

### 5.7 The night before
- Re-skim the 15 pattern templates here, not new problems.
- Sleep. A rested brain pattern-matches far better than a crammed one.
- Set up your environment if it's a shared editor; know how to run Go quickly.

---

## 5.8 The Curated Problem List (Blind-75 style, by pattern)

Solve these ~75 and you'll have seen the core of almost every interview. Do them **in this order**.

**Arrays & Hashing:** Contains Duplicate (217) · Valid Anagram (242) · Two Sum (1) · Group Anagrams (49) · Top K Frequent (347) · Product Except Self (238) · Longest Consecutive Sequence (128) · Encode/Decode Strings (271).

**Two Pointers:** Valid Palindrome (125) · Two Sum II (167) · 3Sum (15) · Container With Most Water (11) · Trapping Rain Water (42).

**Sliding Window:** Best Time to Buy/Sell Stock (121) · Longest Substring Without Repeating (3) · Longest Repeating Character Replacement (424) · Permutation in String (567) · Minimum Window Substring (76) · Sliding Window Maximum (239).

**Stack:** Valid Parentheses (20) · Min Stack (155) · Evaluate RPN (150) · Generate Parentheses (22) · Daily Temperatures (739) · Car Fleet (853) · Largest Rectangle in Histogram (84).

**Binary Search:** Binary Search (704) · Search 2D Matrix (74) · Koko Eating Bananas (875) · Find Min in Rotated Array (153) · Search in Rotated Array (33) · Median of Two Sorted Arrays (4).

**Linked List:** Reverse List (206) · Merge Two Lists (21) · Reorder List (143) · Remove Nth From End (19) · Copy List w/ Random Pointer (138) · Add Two Numbers (2) · Linked List Cycle (141) · Find Duplicate Number (287) · LRU Cache (146) · Merge K Sorted Lists (23).

**Trees:** Invert Tree (226) · Max Depth (104) · Diameter (543) · Balanced Tree (110) · Same Tree (100) · Subtree of Another (572) · LCA of BST (235) · Level Order (102) · Right Side View (199) · Count Good Nodes (1448) · Validate BST (98) · Kth Smallest BST (230) · Construct from Preorder/Inorder (105) · Max Path Sum (124) · Serialize/Deserialize (297).

**Tries:** Implement Trie (208) · Add and Search Word (211) · Word Search II (212).

**Heap / Priority Queue:** Kth Largest in Stream (703) · Last Stone Weight (1046) · K Closest Points (973) · Kth Largest in Array (215) · Task Scheduler (621) · Find Median from Stream (295).

**Backtracking:** Subsets (78) · Combination Sum (39) · Permutations (46) · Subsets II (90) · Combination Sum II (40) · Word Search (79) · Palindrome Partitioning (131) · Letter Combinations of Phone Number (17) · N-Queens (51).

**Graphs:** Number of Islands (200) · Clone Graph (133) · Max Area of Island (695) · Pacific Atlantic (417) · Surrounded Regions (130) · Rotting Oranges (994) · Course Schedule (207) · Course Schedule II (210) · Redundant Connection (684) · Number of Connected Components (323) · Graph Valid Tree (261) · Word Ladder (127).

**Advanced Graphs:** Network Delay Time (743) · Cheapest Flights K Stops (787) · Min Cost to Connect Points (1584) · Swim in Rising Water (778) · Alien Dictionary (269).

**Dynamic Programming (1D):** Climbing Stairs (70) · Min Cost Climbing Stairs (746) · House Robber (198) · House Robber II (213) · Longest Palindromic Substring (5) · Palindromic Substrings (647) · Decode Ways (91) · Coin Change (322) · Max Product Subarray (152) · Word Break (139) · LIS (300) · Partition Equal Subset Sum (416).

**Dynamic Programming (2D):** Unique Paths (62) · LCS (1143) · Best Time to Buy/Sell with Cooldown (309) · Coin Change II (518) · Target Sum (494) · Interleaving String (97) · Edit Distance (72) · Burst Balloons (312) · Regular Expression Matching (10).

**Greedy:** Maximum Subarray (53) · Jump Game (55) · Jump Game II (45) · Gas Station (134) · Hand of Straights (846) · Partition Labels (763) · Valid Parenthesis String (678).

**Intervals:** Insert Interval (57) · Merge Intervals (56) · Non-overlapping Intervals (435) · Meeting Rooms (252) · Meeting Rooms II (253) · Minimum Arrows (452).

**Bit Manipulation:** Single Number (136) · Number of 1 Bits (191) · Counting Bits (338) · Reverse Bits (190) · Missing Number (268) · Sum of Two Integers (371).

**Math & Geometry:** Rotate Image (48) · Spiral Matrix (54) · Set Matrix Zeroes (73) · Happy Number (202) · Plus One (66) · Pow(x,n) (50) · Multiply Strings (43).

---

## Appendix — Complexity Cheat Sheet

### Big-O of common operations (Go)

| Operation | Complexity |
|-----------|-----------|
| Slice index access `s[i]` | O(1) |
| Slice `append` (amortized) | O(1) |
| Slice insert/delete at i (ordered) | O(n) |
| Map get/set/delete | O(1) average |
| Sort (`sort.Slice`, `sort.Ints`) | O(n log n) |
| Binary search | O(log n) |
| Heap push/pop | O(log n) |
| Heap peek | O(1) |
| BFS / DFS over graph | O(V + E) |
| Building a string with `+` in a loop | O(n²) ❌ — use `strings.Builder` |

### Complexity by data structure

| Structure | Access | Search | Insert | Delete |
|-----------|--------|--------|--------|--------|
| Array / Slice | O(1) | O(n) | O(n) | O(n) |
| Hash Map | — | O(1)* | O(1)* | O(1)* |
| Binary Search Tree (balanced) | O(log n) | O(log n) | O(log n) | O(log n) |
| Heap | O(1) peek | O(n) | O(log n) | O(log n) |
| Stack / Queue | O(n) | O(n) | O(1) | O(1) |

\* average case; worst case O(n) for hash collisions.

### Sorting algorithms

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Quicksort | n log n | n log n | n² | log n | No |
| Mergesort | n log n | n log n | n log n | n | Yes |
| Heapsort | n log n | n log n | n log n | 1 | No |
| Go's `sort` | — | n log n | n log n | — | (intro/pattern-defeating quicksort) |

### "What complexity should I aim for?" (from input size)

| n is up to | Target complexity | Typical technique |
|------------|-------------------|-------------------|
| ≤ 12 | O(n!) | permutations / brute backtracking |
| ≤ 20 | O(2ⁿ) | subsets / bitmask DP |
| ≤ 500 | O(n³) | 3 nested loops / interval DP |
| ≤ 5,000 | O(n²) | DP, pairwise |
| ≤ 10⁶ | O(n log n) | sorting, heaps, binary search |
| ≤ 10⁸ | O(n) | hashing, two pointers, sliding window |
| > 10⁸ | O(log n) / O(1) | binary search, math |

---

## Final words

You now have, in one place: the Go you need, every core data structure with a working Go implementation, the 15 patterns that cover almost every interview problem, ~75 curated problems, and a battle-tested interview framework.

The document is the map. The territory is the **reps**. Pick the curated list, solve in Go, re-solve what you miss, narrate out loud, and run mock interviews. Consistency over intensity — 2 focused hours a day for 8 weeks beats one heroic all-nighter.

Good luck. You've got this. 🎯

---
*Built as a self-contained study guide. Verify exact LeetCode problem numbers/constraints on leetcode.com, as problems are occasionally renumbered or updated.*
