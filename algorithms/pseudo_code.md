# Sorting
## Selection Sort
```
sort(A)
    n = A.length
    for i in [0:n]
        min = i
        for j in [i + 1:n]
            if A[j] < A[min]
                min = j
         swap(A[i], A[min])
```

## Insertion Sort
```
sort(A)
    n = A.length
    for i in [0:n]
        for j = i; j > 0 and A[j] < A[j - 1]; --j
            swap(A[j], A[j - 1])
```

## Bubble Sort
```
sort(A)
    n = A.length
    for i in [0:n]
        exchanges = 0
        for j = n - 1; j > i; --j
            swap(A[j], A[j - 1])
            ++exchanges
        if exchanges == 0
            break
```

## Shellsort
```
sort(A)
    n = A.length
    h = 1
    while h < n / 3
        h = 3 * h + 1
    while h >= 1
        for i in [h:n]
            for j = i; j >= h and A[j] < A[j - h]; j -= h
                swap(A[j], A[j - h])
        h = h / 3
```

## Mergesort
```
sort(A)
    Aux = A
    sort(A, Aux, 0, A.length - 1)


sort(A, Aux, lo, hi)
    if hi <= lo
        return
    mid = (hi + lo) / 2
    sort(A, Aux, lo, mid)
    sort(A, Aux, mid + 1, hi)
    merge(A, Aux, lo, mid, hi)


merge(A, Aux, lo, mid, hi)
    for k in [lo:hi + 1]
        Aux[k] = A[k]
    i = lo
    j = mid + 1
    for k in [lo:hi + 1]
        if mid < i
            A[k] = Aux[j++]
        else if hi < j
            A[k] = Aux[i++]
        else if Aux[j] < Aux[i]
            A[k] = Aux[j++]
        else
            A[k] = Aux[i++]
```

## Quicksort
```
sort(A)
    sort(A, 0, A.length - 1)


sort(A, lo, hi)
    if hi <= lo
        return
    j = partition(A, lo, hi)
    sort(A, lo, j - 1)
    sort(A, j + 1, hi)


partition(A, lo, hi)
    i = lo
    j = hi + 1
    v = A[lo]
    while true
        while A[++i] < v
            if i == hi
                break
        while v < A[--j]
            if j == lo
                break
        if j <= i
            break
        swap(A[i], A[j])
    swap(A[lo], A[j])
    return j
```

## Heapsort
- NOTE: uses elements from 1 to n - 1 (rather than 0 to n - 1) to simplify
  index arithmetic
```
sink(A, k, n)
    while 2 * k <= n
        j = 2 * k
        if j < n and A[j] < A[j + 1]
            ++j
        if A[j] <= A[k]
            break
        swap(A[k], A[j])
        k = j


sort(A)
    n = A.length
    for k = n / 2; k >= 1; --k
        sink(A, k, n)
    while n > 1
        swap(A[1], A[n--])
        sink(A, 1, n)
```

## Bucket Sort
- set bucket_size based on size of A, default to 5
```
sort(A, bucket_size)
    if A.length < 10
        // use another sort
    num_buckets = (A.max - A.min / bucket_size) + 1
    Aux // array of arrays num_buckets in size
    for e in A
        Aux[(e - A.min) / bucket_size].push(e)
    j = 0
    for Arr in Aux
        for e in Arr
            A[j++] = e
```

## Counting Sort
```
sort(A)
    if A.length < 10
        // use another sort
    range = A.max - A.min + 1
    Counts // size of range, initialized to 0's
    for e in A
        ++Counts[e - A.min]
    for i in [1:range]
        Counts[i] += Counts[i - 1]
    Aux // size of A
    for a in A
        Aux[--Counts[e - min]] = e
    A = Aux
```


# Binary Search
## Ordered Array
```
binary_search(A, key)
    lo = 0
    hi = A.length - 1

    while lo <= hi
        mid = (hi + lo) / 2
        if key < A[mid]
            hi = mid - 1
        else if A[mid] < key
            lo = mid + 1
        else
            return mid

    return -1
```

## Binary Search Tree
```
contains(BST, key)
    if key == null
        throw
    return get(BST, key) != null

get(BST, key)
    if key == null
        throw
    if BST == null
        return null
    if key < BST.key
        return get(BST.left, key)
    else if BST.key < key
        return get(BST.right, key)
    return BST.value
```


# Binary Search Tree Size
```
size(BST)
    if BST == null
        return 0
    return 1 + size(BST.left) + size(BST.right)
```


# Tree Traversal
## Recursive
### Preorder
```
traverse(root)
    if root == null
        return
    visit(root)
    traverse(root.left)
    traverse(root.right)
```

### Inorder
```
traverse(root)
    if root == null
        return
    traverse(root.left)
    visit(root)
    traverse(root.right)
```

### Postorder
```
traverse(root)
    if root == null
        return
    traverse(root.left)
    traverse(root.right)
    visit(root)
```


## Iterative
### Level Order (Breadth First)
```
traverse(root)
    if root == null
        return
    Queue = 0
    Queue.enqueue(root)
    while Queue != 0
        t = Queue.dequeue()
        visit(t)
        if t.left != null
            Queue.enqueue(t.left)
        if t.right != null
            Queue.enqueue(t.right)
```

### Preorder
```
traverse(root)
    if root == null
        return
    Stack = 0
    Stack.push(root)
    while Stack != 0
        t = Stack.pop()
        visit(t)
        if t.right != null
            Stack.push(t.right)
        if t.left != null
            Stack.push(t.left)
```

### Inorder
```
traverse(root)
    if root == null
        return
    Stack = 0
    t = root
    while t != null
        while t != null
            if t.right != null
                Stack.push(t.right)
            Stack.push(t)
            t = t.left
        t = Stack.pop()
        while Stack != 0 and t.right == null
            visit(t)
            t = Stack.pop()
        visit(t)
        if Stack == 0
            return
        t = Stack.pop()
```

### Postorder
```
traverse(root)
    if root == null
        return
    Stack = 0
    t1 = root
    t2 = root
    while t1 != null
        while t1.left != null
            Stack.push(t1)
            t1 = t1.left
        while t1.right == null or t1.right == t2
            visit(t1)
            t2 = t1
            if Stack == 0
                return
            t1 = Stack.pop()
        Stack.push(t1)
        t1 = t1.right
```


# Tree Printing
## Inorder Recursive
```
print_node(item, h)
    for i in [0:h]
        print(" ")
    print(item)
    print("\n")

print_tree(root, h)
    if root == null
        print_node("*", h)
        return
    print_tree(root.right, h + 1)
    print_node(root.value, h)
    print_tree(root.left, h + 1)
```

## Level Order (Breadth First)
```
print_tree(root)
    if root == null
        return
    Queue = 0
    dummy
    Queue.enqueue(root)
    Queue.enqueue(dummy)
    while Queue != 0
        t = Queue.dequeue()
        if t != dummy
            print(t.item)
            if t.left != null
                Queue.enqueue(t.left)
            if t.right != null
                Queue.enqueue(t.right)
         else
            print("\n")
            if Queue != 0
                Queue.enqueue(dummy)
```

# Priority Queues
## Heap Priority Queue

# Symbol Tables
## Red-Black BST

## Hash Table (Separate Chaining)

## Hash Table (Linear Probing)


# Graph Algorithms
## Depth First Search
```
class DepthFirstSearch
    Graph
    Marked

    DepthFirstSearch(source)
        dfs(source)

    dfs(v)
        Marked[v] = true
        for each w adjacent to v in Graph
            if !Marked[w]
                dfs(w)
```

## Breadth First Search
```
class BreadthFirstSearch
    Graph
    Marked

    BreadthFirstSearch(source)
        bfs(source)

    bfs(v)
        Queue
        Marked[v] = true
        Queue.enqueue(v)

        while Queue != 0
            u = Queue.dequeue()
            for each w adjacent to u in Graph
                if !Marked[w]
                    Marked[w] = true
                    Queue.enqueue(w)
```

## Connected Components (& Union Find)

## Reachability
### Depth First Paths

### Breadth First Paths

## Cycle (Undirected)
- Graph - undirected graph
- Marked - boolean array signifying that the vertex has been visited where size = number of vertices
- EdgeTo - integer array marking parent in  path
- Cycle - stack containing the cycle if found
```
class Cycle
    Graph
    Marked
    EdgeTo
    Cycle

    Cycle()
        for each vertex v in G
            if !Marked(v)
                dfs(-1, v)

    has_cycle()
        return Cycle != 0

    dfs(u, v)
        Marked[v] = true

        for each w adjacent to v in Graph
            if Cycle != 0
                return
            if !Marked[w]
                EdgeTo[w] = v
                dfs(v, w)
            else if w != u
                for x = v; x != w; x = EdgeTo[x]
                    Cycle.push(x)
                Cycle.push(w)
                Cycle.push(v)
```

## Directed Cycle
- Graph - directed graph
- Marked - boolean array signifying that the vertex has been visited where size = number of vertices
- EdgeTo - integer array marking parent in  path
- OnStack - boolean array signifying if the vertex is part of the cycle
- Cycle - stack containing the cycle if found
```
class DirectedCycle
    Graph
    Marked
    EdgeTo
    OnStack
    Cycle

    Cycle()
        for each vertex v in G
            if !Marked(v) and Cycle == 0
                dfs(v)

    has_cycle()
        return Cycle != 0

    dfs(v)
        OnStack[v] = true
        Marked[v] = true

        for each w adjacent to v in Graph
            if Cycle != 0
                return
            else if !Marked[w]
                EdgeTo[w] = v
                dfs(w)
            else OnStack[w]
                for x = v; x != w; x = EdgeTo[x]
                    Cycle.push(x)
                Cycle.push(w)
                Cycle.push(v)
```

## Depth First Order
- Graph - directed graph
- Marked - boolean array that signifies if the vertex has been visited
- PreNumbering - the preorder order number of the vertex
- PostNumbering - the postorder order number of the vertex
- Preorder - a queue containing the vertices in their preorder ordering
- Postorder - a queue containing the vertices in their postorder ordering
- preCounter - used to maintain the current preorder index of the current vertex
- postCounter - used to maintain the current postorder index of the current vertex
```
class DepthFirstOrder
    Graph
    Marked
    PreNumbering
    PostNumbering
    Preorder
    Postorder
    pre_counter
    post_counter

    DepthFirstOrder()
        for each vertex v in G
            if !Marked(v)
                dfs(v)

    dfs(v)
        Marked[v] = true
        PreNumbering[v] = pre_counter++
        Preorder.enqueue(v)
        for each w adjacent to v in Graph
            if !Marked[w]
                dfs(w)
        Postorder.enqueue(v)
        PostNumbering[v] = post_counter++

    reverse_post()
        Stack
        for v in Postorder
            Stack.push(v)
        return Stack
```

## Topological Sort
- Graph - directed graph
- Order - the reverse post order numbering generated by DFS
```
class TopologicalSort
    Graph
    Order

    TopologicalSort()
        if !DirectedCycle(Graph).has_cycle()
            order = DepthFirstOrder(Graph).reverse_post()
```

## Strong Components (Kosaraju-Sharir)

## Minimum Spanning Tree (Prim)

## Minimum Spanning Tree (Kruskal)

## Shortest Paths (Djikstra)

## Shortest Paths (DAGs)

## Shortest Paths (Bellman-Ford)



# String Algorithms

## LSD String Sort
## MSD String Sort
## 3-way String Quicksort
## Trie Symbol Table
## TST
## Substring Search (Knuth-Morris-Pratt)
## Substring Search (Boyer-Moore)
## Substring Search (Rabin-Karp)
## Regular Expression Pattern Matching
## Huffman Compression
## LZW Compression

# General Algorithms
## Divide-and-Conquer
### Maximum Subarray
### Matrix Multiplication
## Permutations
## Combinations
## Partitions
## Backtracking
### n-Queens
### Sudoku