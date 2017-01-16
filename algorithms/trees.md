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















# Implementation

## Iterators (in C++)
- need a parent link in the node to just store a pointer
- useful to use a dummy root node to implement end
- with no parent link, can construct a linear structure (stack, queue, vector, etc) to
  store node pointers in a specific order but this will easily be invalidated

## Handling misses in APIs that take keys as an argument
- Java implementation allows for null, this requires a pointer type in C++
- the misses return null (Java)
- the C++ std library returns an iterator that references an element or is equivalent
  to the end() iterator
- Herb Sutter says for DAG/tree that hands out strong references to data work with shared_ptr<Data>
  (since keys can be data/large data also, makes sense to use them here as well)

## Value semantics
- in general, do not make as much effort to avoid value semantics
- libraries use references for speed but value semantics simplifies
  APIs and can be more understandable

## Options
- APIs use references
- use pair to store key & value
- use smart pointers to store key & value
- add parent pointers to nodes
- to solve the issue of the usage of null in Java API's, instead of storing
  as a pointer, can just return the address of the data as a T* (Non_owning_raw_pointer)