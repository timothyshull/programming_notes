NOTE: Introduction to Algorithms has a more generalized and detailed overview of techniques

# Recursion and Trees
- a function that calls itself
- requires a termination condition
- recursion and trees are tightly connected
- mathematical recurrences and recursion in programs are connected as well
- additional items:
    - divide and conquer
    - dynamic programming
    - tree traversal
    - depth-first (and breadth-first) search

## Recursive Algorithms
- solves a problem by solving one or more smaller instances of the problem
```
int factorial(int N) {
    if (N == 0) return 1;
    return N*factorial(N-1);
}
```
- (the mathematical concept/definition of) recurrence relations are recursively defined functions
- any recursively defined function can be expressed using loops and any computation
  using loops can be defined recursively
- Euclid's algorithm
```
int gcd(int m, int n) {
    if (n == 0) return m;
    return gcd(n, m % n);
}
```
- evaluate prefix expressions (example of a recursive descent parser)
```
char *a; int i;
int eval() {
    int x = 0;
    while (a[i] == ' ') {
        i++;
    }
    if (a[i] == '+') {
        i++;
        return eval() + eval();
    }
    if (a[i] == '*') {
        i++;
        return eval() * eval();
    }
    while ((a[i] >= '0') && (a[i] <= '9')) {
        x = 10 * x + (a[i++] - '0');
    }
    return x;
}
```
- the definition of linked data structures is inherently recursive
- linked list recursive functions:
```
int count(link x) {
    if (x == 0) {
        return 0;
    }
    return 1 + count(x->next);
}
void traverse(link h, void visit(link)) {
    if (h == 0) {
        return;
    }
    visit(h);
    traverse(h->next, visit);
}
void traverseR(link h, void visit(link)) {
    if (h == 0) {
        return;
    }
    traverseR(h->next, visit);
    visit(h);
}
void remove(link& x, Item v) {
    while (x != 0 && x->item == v) {
        link t = x;
        x = x->next;
        delete t;
    }
    if (x != 0) {
        remove(x->next, v);
    }
}
```

## Divide and Conquer
- uses two recursive calls, each split to roughly half of the input
- divide and conquer to find the max
```
Item max(Item a[], int l, int r) {
    if (l == r) {
        return a[l];
    }
    int m = (l + r) / 2;
    Item u = max(a, l, m);
    Item v = max(a, m+1, r);
    if (u > v) {
        return u;
    } else {
        return v;
    }
}
```
- Towers of Hanoi:
```
void hanoi(int N, int d) {
    if (N == 0) {
        return;
    }
    hanoi(N-1, -d);
    shift(N, d);
    hanoi(N-1, -d);
}
```
- draw a ruler (recursive divide and conquer):
```
void rule(int l, int r, int h) {
    int m = (l+r)/2;
    if (h > 0) {
        rule(l, m, h-1);
        mark(m, h);
        rule(m, r, h-1);
    }
}
```
- draw a ruler (non-recursive):
```
void rule(int l, int r, int h) {
    for (int t = 1, j = 1; t <= h; j += j, t++) {
        for (int i = 0; l+j+i <= r; i += j+j) {
            mark(l+j+i, t);
        }
    }
}
```
- the ruler drawing problem can be used to demonstrate a number of relationships
    - n bit numbers
    - effects of preorder vs postorder and bottom-up order
    - fractals
    - combine and conquer

- properties:
    - a recursive function that divides a problem of size n into two independent
      non-empty parts that it solves recursively calls itself less than n times
    - recursive Towers of Hanoi produces a solution with 2 ^ n - 1 moves

- must be aware of the stack during recursion -> both the size and the elements
  stored in each frame
- additional related topics:
    - divide into parts of varying size
    - divide into n parts
    - divide into overlapping parts
    - do varying amounts of work in the non-recursive part of the algorithm

## Dynamic Programming
- divide and conquer's sub-problems are independent
- when the sub-problems are not independent the solutions become more complicated
  because direct recursive approaches take excessive time
- bottom-up dynamic programming:
    - mathematical recurrences work with integers recursively
    - these functions can be solved by computing all of the function values in order
      starting with the smallest and computing the value at each step using values
      from previous steps
    - (possibly uses an array to store previous values and access them)
- top-down dynamic programming:
    - simpler corollary to bottom-up dynamic programming (at same cost or less cost)
    - the recursive program saves each computed value as its last action and
      checks for the computed value to avoid re-computation as its first action
    - the running time is reduced to linear running time
    - sometimes called memoization
- dynamic Fibonacci:
```
int F(int i) {
    static int knownF[maxN];
    if (knownF[i] != 0) {
        return knownF[i];
     }
    int t = i;
    if (i < 0) {
        return 0;
    }
    if (i > 1) {
        t = F(i-1) + F(i-2);
    }
    return knownF[i] = t;
}
```
- recursive knapsack:
```
int knap(int cap) {
    int i, space, max, t;
    for (i = 0, max = 0; i < N; i++) {
        if ((space = cap-items[i].size) >= 0) {
            if ((t = knap(space) + items[i].val) > max) {
                max = t;
            }
        }
    }
    return max;
}
```
- non-recursive knapsack:
```
int knap(int M) {
    int i, space, max, maxi = 0, t;
    if (maxKnown[M] != unknown) return maxKnown[M];
    for (i = 0, max = 0; i < N; i++)
        if ((space = M-items[i].size) >= 0)
            if ((t = knap(space) + items[i].val) > max) {
                max = t;
                maxi = i;
            }
        }
    }
    maxKnown[M] = max; itemKnown[M] = items[maxi];
    return max;
}
```
- properties:
    - dynamic programming reduces the running time of a recursive function to be at most
      the time required to evaluate the function for all arguments less than or equal to
      the given argument, treating a recursive call as constant
- top-down vs bottom-up
    - top-down -> save known values
    - bottom-up -> precompute known values
    - prefer top-down because:
        - it is a mechanical transformation of a natural problem solution
        - the ordering of sub-problem computation takes care of itself
        - may not need to compute all of the answers to all of the sub-problems


### Additional Notes
- See Introduction to Algorithms for a more detailed discussion
- NOTE: dynamic programming is characterized by:
    - the overlap between sub-problems
    - optimal sub-structure (an optimal solution can be constructed efficiently from
      optimal solutions of its sub-problems)
    - (non-overlapping sub-problems leads to divide and conquer solutions)
- top-down:
    - solve the top of the input tree first in terms of the sub-problems recursively and store
      solutions to sub-problems in a table (memoization)
```
return_type memoization(p, n) {
    let r[0...n] be a new array
    for (i = 0 to n) {
        r[i] = some invalid value;
    }
    return memoize_aux(p, n, r);
}

return_type memoize_aux(p, n, r) {
    if (r[n] is defined (i.e. greater than 0 or a valid value)) {
        return r[n];
    } else {
        // this is just generic to signal the action of computing smaller values
        // and storing them/returning them
        q = -infinity; // (i.e. invalid value)
        for (i = 1 to n) {
            q = recursively calculate q
        }
        r[n] = q;
        return q;
    }
```

- bottom-up:
    - start by solving sub-problems first and then solve larger and larger problems (using
      a tabular form (tabulation))
```
return_type bottom_up(p, n) {
    let r[0...n] be a new array
    r[0] = 0 // base case
    for (j = 1 to n) {
        q = -infinity // (i.e. invalid value)
        for (i = 1 to j) { // working from sub-problems upward
            calculate q
        }
        r[j] = q;
    }
    return r[n]
}
```


## Trees
- mathematical abstraction:
    - often occurs implicitly in algorithms
    - used to describe dynamic properties of algorithms
    - build concrete realizations of trees for problem solution
- the union find family of solutions to the connectivity problem generates a tree structure
- types of trees:
    - in decreasing generality:
        - trees
        - rooted trees
        - ordered trees
        - m-ary trees and binary trees
- definitions:
    - vertex: (node) has a name (ID) and can carry other information
    - edge: a connection between two vertices (nodes) (also carries information in graph problems)
    - path: a list of distinct vertices with connections
    - tree: a nonempty collection of vertices and edges that satisfy the following properties
        - exactly one path connecting any two nodes
        - NOTE: if there is more than one path between a pair of nodes or no path
          then it is a graph
    - forest: a disjoint set of trees
    - rooted tree: one node is designated as the root (generally implied by the term tree)
    - free tree: more general structure (no designated root)
    - subtree: a tree delineated by taking a non-root node in a rooted tree as the root
    - parent, children, grandparent, sibling
    - leaves (terminal nodes): nodes with no children
    - nonterminal nodes: nodes with children
    - ordered tree: a rooted tree with the child at each node specified with a position
    - m-ary tree: a tree where each nonterminal node must have a specific number of children
    - general tree:
    - external node: node with no children
    - internal node: node with n children
    - binary tree:
        - an external node or an internal node connected to a pair of binary trees (the
          left and right subtree)
    - m-ary tree:
        - an external node or an internal node connected to a sequence of M trees
          that are also m-ary trees
    - tree/ordered tree: a node (the root) connected to a sequence of disjoint trees (i.e. a forest)
    - rooted tree/unordered tree: root connected to a multiset fo rooted trees (unordered forest)
    - graph: set of nodes with a set of edges that connect distinct pairs of nodes (with at most one
      edge connecting any pair of nodes (NOTE: edges have directions so 0,1 is distinct from 1,0))
    - simple path: a sequence of edges defining a path between nodes with no node appearing tqice
    - connected graph: a simple path connecting any pair of nodes
    - cycle: a connected graph with an equivalence between the first and final nodes
 - properties:
    - for a tree, there is a exactly one path between the root and each of the other nodes
      in the tree (this implies no direction for edges, edges point away from the root)
    - there is a one-to-one correspondence between binary trees and ordered forests
    - ordered trees can represent unordered trees (tree isomorphism problem)
- NOTE: there are multiple representations of trees
    - just child node links
    - child node links and a parent node link
    - there is an equivalence between representing the children of a node as a linked list
      and a corresponding binary tree (review this and consider it)

## Mathematical Properties of Binary Trees
- properties:
    - a binary tree with N internal nodes has N + 1 external nodes
    - a binary tree with N internal nodes has 2 * N link; N - 1 links to internal nodes
      and N + 1 links to external nodes
    - the external path length of a binary tree with N internal nodes is 2 * N greater
      than the internal path length
    - the height of a binary tree with N internal nodes is at least lg(N) and at most N - 1
    - the internal path length of a binary tree with N internal nodes is at least N * lg(N / 4) and
      at most N * (N - 1) / 2
- definition:
    - level: a node's level is 1 higher than the level of the parent (with the root at 0)
    - height: max of the levels of a tree's nodes
    - path length: the sum of the levels of the tree's nodes
    - internal path length: sum of the levels of the all of the tree's internal nodes
    - external path length: sum of the levels of all of the tree's external nodes

## Tree Traversal
- traversal ordering:
    - preorder:
        - current node
        - left subtree
        - right subtree
    - inorder:
        - left subtree
        - current node
        - right subtree
    - postorder:
        - left subtree
        - right subtree
        - current node
    - preorder - operate on current node (i.e. visit) then visit the left subtree then visit the right subtree
- recursive tree traversal (preorder):
```
void traverse(link h, void visit(link)) {
    if (h == nullptr) {
        return;
    }
    visit(h);
    traverse(h->l, visit);
    traverse(h->r, visit);
}
```
- pay attention to the state of the call stack for these
- tree traversal (non-recursive, preorder):
```
void traverse(link h, void visit(link)) {
    Stack<link> s(max);
    s.push(h);
    while (!s.empty()) {
        visit(h = s.pop());
        if (h->r != 0) {
            s.push(h->r);
        }
        if (h->l != 0) {
            s.push(h->l);
        }
    }
}
```
- for ordering in the non-recursive version:
    - preorder:
        - push right, left, node
        - push right, node, left
        - push node, right, left
- level order traversal:
```
void traverse(link h, void visit(link)) {
    Queue<link> q(max);
    q.put(h);
    while (!q.empty()) {
        visit(h = q.get());
        if (h->l != 0) {
            q.put(h->l);
        }
        if (h->r != 0) {
            q.put(h->r);
        }
    }
}
```
- these approaches are important because they correspond to different
  orderings for processing work to be done
- level order does not inherently correspond to a recursive implementation
  that corresponds to the recursive structure of a tree
- each of these traversal approaches correspond to forests as well (if you think
  of a forest as a tree with an imaginary root)
    - preorder: visit the root then each of the subtrees
    - postorder: vist the subtrees then the root
    - level order: same as for binary trees

## Recursive Binary Tree Algorithms
- recursive nature of the tree as a data structure leads to recursive algorithms for
  tree operations
- tree operations:
```
int count(link h) {
    if (h == nullptr) return 0;
    return count(h->l) + count(h->r) + 1;
}

int height(link h) {
    if (h == nullptr) return -1;
    int u = height(h->l), v = height(h->r);
    if (u > v) return u+1; else return v+1;
}

void print_node(Item x, int h) {
    for (int i = 0; i < h; i++) cout << "  ";
    cout << x << endl;
}
void show(link t, int h) {
    if (t == nullptr) { printnode('*', h); return; }
    show(t->r, h+1);
    printnode(t->item, h);
    show(t->l, h+1);
}
```
- for print, the effect of preorder vs. postorder is important to consider
- tournament (tree): is a form of binary heap/min (max) tree
- joining tournaments:
    - create new node
    - make the new node's left link point to one tournament
    - make the new node's right link point to the other
- constructing a tournament from an array:
```
struct node
  { Item item; node *l, *r;
    node(Item x)
      { item = x; l = 0; r = 0; }
  };
typedef node* link;
link max(Item a[], int l, int r) {
    int m = (l+r)/2;
    link x = new node(a[m]);
    if (l == r) return x;
    x->l = max(a, l, m);
    x->r = max(a, m+1, r);
    Item u = x->l->item, v = x->r->item;
    if (u > v) {
        x->item = u;
    } else {
        x->item = v;
    }
    return x;
}
```
- prefix expression parse tree:
```
char *a; int i;
struct node {
    Item item; node *l, *r;
    node(Item x) {
        item = x;
        l = 0;
        r = 0;
    }
};
typedef node* link;
link parse() {
    char t = a[i++]; link x = new node(t);
    if ((t == '+') || (t == '*')) {
        x->l = parse();
        x->r = parse();
    }
    return x;
}
```

## Graph Traversal
- graph depth-first search is a generalization of tree traversal methods
- depth-first search:
```
void traverse(int k, void visit(int)) {
    visit(k);
    visited[k] = 1;
    for (link t = adj[k]; t != 0; t = t->next) {
        if (!visited[t->v]) {
            traverse(t->v, visit);
        }
    }
}
```
- can also define a DFS method that uses an explicit stack
- gives a linear time solution to the connectivity problem
- for large graphs it may still be preferable to use the union find technique
  because the whole graph takes space proportional to E whereas union find
  takes space proportional to V
- breadth-first search:
```
void traverse(int k, void visit(int)) {
    Queue<int> q(V*V);
    q.put(k);
    while (!q.empty()) {
        if (visited[k = q.get()] == 0) {
            visit(k); visited[k] = 1;
            for (link t = adj[k]; t != 0; t = t->next) {
                if (visited[t->v] == 0) q.put(t->v);
            }
        }
    }
}
```
- properties:
    - DFS requires time proportional to num vertices + num edges when using
      an adjacency list representation