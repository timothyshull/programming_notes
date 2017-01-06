# Graph Properties and Types
- studied extensively in mathematics
- examples:
    - maps
    - hypertexts
    - circuits
    - schedules
    - transactions
    - matching
    - networks
    - program structure
- any linked data structure is a representation of a graph, and many familiar
  algorithms for processing trees and other linked structures are special cases
  of graph algorithms
- performance characteristics are difficult to analyze because:
    - the cost depends on both nodes and edges
    - accurate models of the range of types of graphs are difficult to develop

## Glossary
- definitions:
    - graph: set of vertices and a set of edges connecting distinct pairs of vertices
    - vertex (node): a single element in a graph (vertex for graph, node for representation)
    - edge (arc, link): a single connection between two distinct elements in a graph
    - parallel edges: duplicate edges
    - multigraph: a graph containing parallel edges
    - self-loop: an edge that connects a vertex to itself
    - simple graph: a graph with no self-loops or parallel edges
    - adjacent: two vertices that are connected are adjacent to one another
    - incident: an edge is incident to both vertices that it connects
    - subgraph: a subset of a graph's edges that also constitutes a graph
    - induced subgraph: an identified subset of a graph's vertices and the associated edges
    - graph drawing: placement of a graph's vertices on a plane and graphically depicting the graph in this manner
    - planar graph: a graph that can be drawn in a plane without any edges crossing
    - Euclidean graphs: a graph that specifically relates vertices in a plane with relevant distances
    - isomorphic: two graphs are isomorphic if their labels can be interchanged to make the set of edges identical
    - path: a sequence of vertices in which successive vertices are adjacent (or the set of edges that defines these connections)
    - simple path: a path with distinct edges and vertices
    - cycle: a simple path where the initial and final vertices are the same vertex
    - cyclic path: a path with the same initial and final vertex (that is not necessarily simple)
    - tour: a cyclic path that includes every vertex
    - length: the number of edges in a path or a cycle
    - disjoint: two paths are disjoint if they have no vertices in common (other than possibly their endpoints)
    - vertex disjoint: another name for the general definition of disjoint
    - edge disjoint: two paths that have no edges in common (a slightly stronger condition)
    - connected graph: a graph with a path from every vertex to every other vertex
    - unconnected graph: a graph that consists of connected components that is not connected
    - connected components: maximally connect subgraphs
    - tree: an acyclic connected graph
    - forest: a set of trees
    - spanning tree: a subgraph of a connected graph that contains all of that graph's vertices and is a single tree
    - spanning forest: a subgraph of a graph that contains all of the graph's vertices and is a forest
    - complete graph: a graph with all edges present
    - complement: a graph that is the complete graph with all of the edges of the original removed
    - union: the graph created by creating a union of both graphs sets of edges
    - clique: a complete subgraph
    - degree (valency) (of a vertex): the number of edges incident to a vertex, with loops counted twice
    - density: the average vertex degree, or 2 * E/V
    - dense graph: a graph whose average vertex degree is proportional to V (or E is proportional to V^2)
    - sparse graph: a graph whose complement is dense
    - bipartite graph: a graph that can be divided into two sets of vertices such that all edges connect a vertex in one set with a vertex in the other
    - undirected graph: graphs with no direction applied to edges (i.e. all edges are bidirectional)
    - directed graph (digraph): a graph with edges that are one-way or directed, i.e. a pair specifies being able to move from one vertex to the other but not move in reverse
    - directed edges: an edge that specifies an ordering, i.e. the ability to move from one vertex to another but not vice versa
    - source (head): the first vertex in a directed edge
    - destination (tail): the second vertex in a directed edge
    - indegree: a measurement of the number of edges that include a given vertex as the destination
    - outdegree: a measurement of the number of edges that include a given vertex as the source
    - directed cycle: a cycle in a directed graph in which all adjacent vertex pairs appear in the order indicated by the directed edges
    - directed acyclic graph (DAG): a directed graph with no directed cycles (not the same as an acyclic undirected graph)
    - acyclic undirected graph: aka tree
    - underlying undirected graph: a graph defined by the set of edges in a digraph interpreted without a direction
    - weights: a distance or cost associated with an edge
    - weighted graph: a graph that has weighted edges
    - networks: weighted digraphs

- properties:
    - a graph with V vertices has at mose V * (V - 1) / 2 edges (V ^ 2 possible pairs of edges
      includes self-loops and counts twice for each edge)
    - identifying isomorphic graphs is difficult because there a V! ways to label vertices
    - a graph G with vertices V is a tree if and only if (NOTE: each of these conditions is sufficient on their own):
        - G has V - 1 edges and no cycles
        - G has V - 1 edges and is connected
        - Exactly one simple path connects each pair of vertices on G
        - G is connected, but removing any edge disconnects it
    - all graphs that have V vertices are subgraphs of the complete graph that has V vertices
    - the total number of different graphs with V vertices is 2 ^ (V * (V- 1) / 2)

## Graph ADT
- general graph ADT:
```
struct Edge {
    int v, w;
    Edge(int v = -1, int w = -1) : v(v), w(w) { }
};

class Graph {
private:
    // Implementation-dependent code
public:
    Graph(int, bool); // int -> memory allocation
    ~Graph();
    int V() const;
    int E() const;
    bool directed() const;
    int insert(Edge);
    int remove(Edge);
    bool edge(int, int);
    class adjIterator {
    public:
        adjIterator(const GRAPH &, int);
        int beg();
        int nxt();
        bool end();
    };
};
```
- this is the simplest interface to facilitate basic operations and not a general purpose interface
- a general graph interface needs to account for self-loops and parallel edges
- graph iterator example:
```
template <class Graph>
vector <Edge> edges(Graph &G) {
    int E = 0;
    vector <Edge> a(G.E());
    for (int v = 0; v < G.V(); v++) {
        typename Graph::adjIterator A(G, v);
        for (int w = A.beg(); !A.end(); w = A.nxt())
          if (G.directed() || v < w)
            a[E++] = Edge(v, w);
    }
    return a;
}
```
- it is often the most useful to just consider a graph as a set of its edges
- a client to print graphs:
```
template <class Graph>
void IO<Graph>::show(const Graph &G) {
    for (int s = 0; s < G.V(); s++) {
        cout.width(2); cout << s << ":";
        typename Graph::adjIterator A(G, s);
        for (int t = A.beg(); !A.end(); t = A.nxt()) { cout.width(2); cout << t << " "; }
        cout << endl;
    }
}
```
- generalized operations on graphs:
    - compute the value of some measure of a graph
    - compute some subset of the edges of a graph
    - answer queries about some property of a graph
- graph processing IO interface:
```
template <class Graph>
class IO {
public:
    static void show(const Graph &);
    static void scanEZ(Graph &);
    static void scan(Graph &);
};
```
- usually include preprocessing (generally in the ctor) and public member functions
  for querying
- static graphs: graphs with a fixed number of vertices and edges
- will not address dynamic graphs which leads to online algorithms aka dynamic algorithms
- graph connectivity interface:
```
template <class Graph>
class CC {
private:
    // implementation-dependent code
public:
    CC(const Graph &);
    int count();
    bool connect(int, int);
};
```
- graph processing client:
```
#include <iostream.h>
#include <stdlib.h>
#include "GRAPH.cc"
#include "IO.cc"
#include "CC.cc"
main(int argc, char *argv[]) {
    int V = atoi(argv[1]);
    GRAPH G(V);
    IO<GRAPH>::scan(G);
    if (V < 20) IO<GRAPH>::show(G);
    cout << G.E() << " edges ";
    CC<GRAPH> Gcc(G);
    cout << Gcc.count() << " components" << endl;
}
```

## Adjacency Matrix Representation
- a V x V matrix of boolean values representing a graph with 0 in a row x column location
  if there is no edge between vertices and 1 if there is an edge
- adjacency matrices are better suited to dense graphs
- adjacency matrix graph representation:
```
class DenseGRAPH
{ int Vcnt, Ecnt; bool digraph;
  vector <vector <bool> > adj;
public:
  DenseGRAPH(int V, bool digraph = false) :
    adj(V), Vcnt(V), Ecnt(0), digraph(digraph)
    {
      for (int i = 0; i < V; i++)
        adj[i].assign(V, false);
    }
  int V() const { return Vcnt; }
  int E() const { return Ecnt; }
  bool directed() const { return digraph; }
  void insert(Edge e)
    { int v = e.v, w = e.w;
      if (adj[v][w] == false) Ecnt++;
      adj[v][w] = true;
      if (!digraph) adj[w][v] = true;
    }
  void remove(Edge e)
    { int v = e.v, w = e.w;
      if (adj[v][w] == true) Ecnt--;
      adj[v][w] = false;
      if (!digraph) adj[w][v] = false;
    }
  bool edge(int v, int w) const
    { return adj[v][w]; }
  class adjIterator;
  friend class adjIterator;
};
```
- processing all vertices adjacent to a given vertex requires time proportional to V
- adjacency matrix iterator:
```
class DenseGRAPH::adjIterator
{ const DenseGRAPH &G;
  int i, v;
public:
  adjIterator(const DenseGRAPH &G, int v) :
    G(G), v(v), i(-1) { }
  int beg()
    { i = -1; return nxt(); }
  int nxt()
    {
      for (i++; i < G.V(); i++)
        if (G.adj[v][i] == true) return i;
      return -1;
    }
  bool end()
    { return i >= G.V(); }
};
```
- saving space:
    - undirected graphs are symmetric, i.e. `a[v][w]` always equals `a[w][v]` so only half of the matrix needs to be stored
    - can use n X n bitsets (vector<bool> is suggested but this data type is problematic in C++)
- associating vertex names with integers from 0 - n:
    - hashing
    - adding an index field to each node in an existence table TST
- two seemingly different graphs can potentially be equivalent through vertex renaming
- do not use adjacency matrices for large sparse graphs (due to the use of V^2 space)

## Adjacency List Representation
- preferred standard representation
- for undirected graphs, add an edge by adding vertex v to w's adjacency list
  and w to v's adjacency list
- be sure to include destructors, copy constructors (and move constructors, assignment operators)
  for various implementations
- includes array (or vector) of linked lists
```
// Program 17.9 here Sparse_multigraph
// Program 17.10 here Sparse_multigraph_iterator
```
- basic implementations do not remove parallel edges on edge insertion
  (constant time vs. time proportional to V to remove parallel edges)
- this approach is not suitable for large adjacency lists
- can associate non-integer vertex names with integers but cannot determine
  isomorphism because of the difficult of the graph isomorphism problem
- numerous representations of the same graph even for a set numbering
- primary advantage is the use of space E + V (as opposed to V^2)

## Variations, Extensions, and Costs
- methods for improvement:
    - adjacency matrices and adjacency lists can be extended easily
    - additional features with advanced data structures
    - graph processing with task specific classes
- examples:
    - digraphs may need a method for representing edges coming into a vertex
    - weighted graphs and networks use structures containing additional Edge information
    - can use vertex indexed vectors to associate additional information with vertices
    - often use special classes for graph processing with additional data structures
      to hold vertex information
```
// Program 17.11 Degree class
```
- "fat" interfaces can have serious drawbacks for graph processing
- an alternative to separate graph processing classes is inheritance (can be an exercise)
```
// Table 17.1 Worst-case cost of graph processing algorithms
```
- find edge and remove edge are often used and so it is sometimes beneficial
  to use auxiliary symbol tables to improve search, etc (std::map, std::set, etc)
- must use pointers for auxiliary symbol tables, also need doubly linked lists for efficiency
- vertex removal is also expensive because any edges referencing that vertex must
  also be removed
- these operations are omitted from the text because they all relate to previous exercises, algorithms, etc
- static graphs can replace the linked lists with vectors which can improve worst case run times
- graph processing algorithms are not linear in time if an adjacency matrix is used to
  represent a sparse graph or if an adjacency list is used to represent an extremely sparse graph
  (ignored due to standard assumptions in text)

## Graph Generators
- can generate graphs from user input or randomized edges
```
// Program 17.12 Random graph generator
```
- generate pairs from 0 to V - 1 (will likely contain self loops and parallel edges)
- removing parallel edges will generally lead to a more dense graph
- can also randomly generate the number of vertices
```
// Program 17.13 Random graph generator
```
- have a well-studied mathematical/probabilistic background
- can choose a probability, p, such that p = 2 * E / V (V - 1)
- k-neighbor graph:
    - randomly pick a vertex, v, then randomly pick the second within a constant
      k of v (wrapping from V - 1 to 0)
- Euclidean neighbor graph:
    - generate V points in a plane with random coordinates between 0 and 1 and
      then generates edges for all points within a distance d of each other
    - if a sparse graph is generated, the graph is not likely to be connected
- transaction graph:
    - used to model connections between elements with id's (i.e. telephone calls, etc)
- can use a symbol table to build a graph by equating a symbol with its index
```
// Program 17.14 Building a graph from pairs of symbols
```
- function call graph:
    - a computer program can be represented as a graph with functions as
      vertices and edges representing when a function is called from within
      another function
    - can be generated manually or instrumented within compilers
- can use a TST to index string keys for a graph
```
// Program 17.15 Symbol indexing for vertex names
```
- often need to determine a way to map symbolic names to vertex indices to
  represent a problem as a graph
- degrees-of-separation graph:
    - collection of subsets from V, item is given a degree of separation from
      a given vertex, v, indicating the number of incident steps it takes to arrive
      at that item from v
- interval graph:
    - intervals are defined on a number line, assigned as vertices, and edges
      are defined if the intervals intersect
- de Brujin graph:
    - digraph with V as a power of 2, with one vertex for each non-negative integer less
      than V, and edges from each vertex i to 2i and to (2i + 1) % lg(V)

## Simple, Euler, and Hamiltonian Paths
- simple DFS path search
```
// Program 17.16 Simple path search
```
- some simple path problems just want to know if a path exists, others want to
  find a specific path
- can print a path using DFS by switching the order of the inputs and printing
  the edge after the recursive call (switch order because otherwise the printed
  order would print the reverse path)
- can use this same format to use a client-defined visit function

- properties:
    - we can find a path connection two given vertices in a graph in linear time

- worst case DFS for adjacency lists checks all of the edges twice (once in each direction)
- linear in graphs means within a constant factor of V + E
- cannot refer to V^2 as linear and adjacency matrix algorithms that check all graph
  edges are always V^2
- paths may be found before examining all edges, thus reducing running time
- Hamilton path -> a path between two vertices containing each vertex exactly once
- Hamilton tour -> a Hamilton path that returns to the original vertex
```
// Program 17.17 Hamilton path
```

- properties:
    - a recursive search for a Hamilton tour could take exponential time
    - if there is not a simple path or a Hamilton path from t to w, then there is
      not one for v to w that goes through t (where v is connected to t)

- the result is that every single path in a graph may need to be checked, resulting
  in a (V - 1)! number of recursive calls or roughly (V / e) ^ V
- Euler path -> a path between two vertices that uses each edge exactly once
- Euler tour -> a cyclic path that uses each edge exactly once
- generally considered the beginning of graph theory
- Bridges of Konigsberg

- properties:
    - a graph has an Euler tour if and only if it is connected and all its
      vertices are of even degree
    - corollary - a graph has an Euler path if and only if it is connected
      and exactly two of its vertices are of odd degree

```
Program 17.18 Euler path existence
```
- with induction, to have an Euler tour, need to leave the start vertex on one
  edge and return on another, etc
- to check if an Euler path/tour exists, can check the vertex degrees with a matrix in V ^ 2 time,
  time proportional to E with an edge set, and time proportional to V with a vertex indexed
  vector representation
- to find an Euler path, run into the same factorial time issue as above, but
  can reduce running time by testing edges before following them

- properties:
    - an Euler tour in a graph (that has an Euler tour) can be found in linear time

- the process follows a cycle by pushing the vertices onto a stack and backtracks on failure
  to check additional side paths
```
Program 17.19 Linear-time Euler path
```
- few graphs have Euler tours and, as such, these algorithms are not broadly
  applicable
- DFS is the most common generalized graph search method

## Graph Processing Problems
- often useful to first determine how difficult a problem is to solve
- problem types:
    - simple connectivity - checks if there is a path connecting every pair of
      vertices (generally easy)
    - strong connectivity in digraphs - is there a directed path connecting
      every pair of vertices (generally easy)
    - transitive closure - the set of vertices that can be reached by following
      directed edges from each vertex in a digraph (generally easy)
    - minimum spanning tree - in a weighted graph, the minimum-weight set of edges
      that connects all vertices (classical problem)
    - single-source shortest paths - shortest paths connecting a vertex with each
      other vertex in a weighted digraph (network) (difficult if edge weights can
      be negative)
    - planarity - draw a graph without any edges (lines) intersecting -> Kuratowski,
      Tarjan
    - matching - largest subset of a graph's edges with no two connected to the
      same vertex (also bipartite matching)
    - even cycles in digraphs - cycle of even length in a digraph (difficult to prove)
    - assignment - bipartite weighted matching -> perfect matching of minimum weight
      in a bipartite graph
    - general connectivity - minimum number of edges that can be removed that will
      separate a graph into two disjoint parts (difficult, related to network flow)
    - mail carrier - a tour with a minimal number of edges that uses every vertex at least
      once (more difficult than Euler tour and less difficult that Hamilton tour)

- intractable problems:
    - longest path - longest simple path connecting two vertices (NP-hard)
    - colorability - find a method of assigning k colors to each of the vertices
      in a graph such that no edge connects two vertices of the same color (easy for k
      of 2, NP-hard for k of 3)
    - independent set - largest subset of the vertices such that not two are connected
      (NP-hard)
    - clique - size of the largest clique (complete subgraph)
    - graph isomorphism - make two graphs identical by renaming vertices

- see table 17.2 for more info



# Graph Search
- almost all graph problems follow the abstract model set up by graph search

## Exploring a Maze
- maze = graph, passage = edge, intersection = vertex
- Tremaux exploration
    - setup - lights (off) in each intersection and doors (closed) at the end
      of each passage
    - goal - turn on all lights and open all doors
    - process:
        1. if no closed doors -> 3.
           else open any closed door
        2. if the light at the end of the passage is lit, try another door
           else (if dark) follow passage and unroll string
        3. if all doors open
               if at start? then stop
               else return to previous intersection (re-rolling string) -> 1.
- properties:
    - Tremaux maze exploration lights all lights and opens all doors and returns
      to maze start

## Depth-first Search
- recursive DFS:
    - mark a vertex as visited and recursively visit all adjacent vertices
```
Program 18.1 Depth-first search of a connected component
```
- NOTE: generates a tree-like call stack, visits each edge twice, once as v-w, once
  as w-v
- difference with Tremaux exploration:
    - results in a slightly different ordering
    - moving on edge v - w, edges w - * are not examined (i.e. w - v) which
      is ignored when reached because it is marked
    - for a direct correspondence, close doors on the way into a passage and
      open doors on the way out of a passage
- adjacency lists and adjacency matrices result in a different ordering of visitation
  i.e. order in lists vs. numerical order

- NOTE: DFS and the following sections use vector ord (order) to mark the order
  in which the vertex was visited

## Graph Search ADT Functions
- following graph edges from vertex to vertex only leads to visiting all
  vertices in a connected component
```
// Program 18.2 Graph search
```
- general search process (using an ADT):
    - find an unmarked vertex
    - visit and mark as visited all the vertices in the connected component
      that contains the start vertex
    - the method for finding the next vertex in the next connected component
      is not strictly specified but generally is found by increasing scan
    - pass an edge rather than the next vertex to know the start point
```
// Program 18.3 Derived class for depth-first search
```
- properties:
    - a graph search function checks each edge and marks each vertex in a graph
      if and only if the search function that it uses marks each vertex and
      checks each edge in the connected component that contains the start vertex
    - DFS of a graph represented with an adjacency matrix requires time proportional
      to V^2
    - DFS of a graph represented with adjacency lists requires time proportional
      to V + E
    - general DFS-like graph search functions, with time f(V, E), take time E + V * f(V, E)
- use the convention of initializing each index of the visited vertex vector to -1
  and positive (1) when visited
- DFS creates a tree of the nodes that are visited in the order they are visited

## Properties of DFS Forests
- trees used to represent recursive DFS function calls give insight into DFS
- representations:
    - external nodes as moments when a node was skipped as marked
        - tree represents graph
        - preorder traversal of the tree == DFS traversal of graph (and Tremaux maze traversal)
- NOTE: uses a vector, st, to record parent links
- edge classification:
    - edges representing a recursive call (tree edges)
    - edges connection a vertex to an ancestor not its parent (back edges)
    - links for v to w are classified as:
        - a tree link if w is unmarked
        - a parent link if `st[w]` is v
        - a back link if `ord[w] < ord[v]`
        - a down link if `ord[w] > ord[v]`

- a DFS forest can be used to represent the dynamics of DFS for a given graph
- connected components correspond to different trees in the forest, and the st
  vector contains a representation of these relationship (consider UF graph representation)
- most resulting considerations are trivial for small graphs but become important for
  large graphs:
    - recursive depth
    - paths
    - number of adjacent vertices to a given vertex

## DFS Algorithms
- cycle detection:
    - a graph is acyclic if and only if no back or down edges are encountered in DFS
    - can exit early - will find a cycle or finish the search without finding one
      before examining V edges (time proportional to V for list repr, V^2 for matrix repr)
- simple path:
    - use DFS with a visited/marked vector (17.7)
    - linear time
- simple connectivity:
    - connected if recursive DFS is only called once in graph search
    - number of connected components == the number of times recursive DFS function
      is called in graph search
    - linear time
```
// Program 18.4 Graph connectivity
```
- two-way Euler tour:
    - use Tremaux exploration but go back and forth each back link and ignore down
      links (or ignore back links and go back and forth down links)
```
// Program 18.5 Two-way Euler tour
```
- spanning forest:
    - find a set of V - 1 edges that connect the vertices of a connected graph
      for C connected components, find a spanning forest with V - C edges
    - see 18.3
- vertex search:
    - how many vertices are in the same connected component as a given vertex
    - just run DFS from the start vertex and count the number of marked vertices
- two-colorability (bipartiteness) odd cycle
    - assign one of two colors to each vertex such that no edge connects two vertices
      of the same color
    - equivalent to checking if a graph has a cycle of odd length (odd length cycle is not
      two colorable)
    - alternate colors while moving down a DFS tree and check for inconsistencies
      on the way back up
```
// Program 18.6 Two-colorability (bipartiteness)
```

!!!NOTE - revisit this section to fully understand the tree labeling/generation
## Separability and Biconnectivity
- general algorithms look at graph connectivity if an edge is removed and
  if a vertex if removed
- definitions:
    - bridge: edge in a connected graph, that, if removed, would result in
      two disjoint subgraphs
    - edge-connected: a graph with no bridges
    - edge-separable: not edge connected
    - separation edges: bridges
    - edge-connected/bridge-connected components: maximal subgraphs with no bridges
    - articulation point: vertex in a connected graph, that, if removed, would result in
    - separation vertex or cut vertex: articulation point
    - biconnected: every pair of vertices is connected by two disjoint paths
    - k-connected: at least k vertex disjoint paths connecting every pair of vertices in a graph
    - vertex connectivity: minimum number of vertices that need to be remove to separate it
      into two pieces
    - k-edge connected: at least k edge-disjoint paths connecting every pair of vertices
      in the graph
    - edge connectivity: mminimum number of edges that need to be removed to separate it into
      two pieces
- properties:
    - a tree edge in a DFS tree is a back edge if and only if there are no back edges
      that connect a descendant of the forward vertex with an ancestor of that vertex
    - a graph's bridges can be found in linear time
    - a graph is biconnected if an only if it has no articulation points
    - a graph's articulation points and biconnected components can be found in linear time
```
// Program 18.7 Edge connectivity
```
- vertex removal implies removal of all incident edges
- related problems:
    - st-connectivity: minimum number of edges or vertices that will separate two
      vertices, s and t, in a graph
    - general connectivity: k-connectivity, k-edge-connectivity, edge connectivity,
      vertex connectivity

## Breadth-first Search
- breadth-first search yields shortest paths
- swap DFS's stack with a queue
- general steps:
    - start by placing the start vertex on the queue
    - take edges from the queue until finding one that points to an unvisited
      vertex
    - visit vertex, enqueue all of that vertex's adjacent vertices
- properties:
    - during BFS, vertices enter and leave the queue in the order of their distance
      from the start vertex
    - BFS visits in V^2 for matrix repr and V + E for list repr
```
// Program 18.8 Breadth-first search
// Program 18.9 Improved BFS
```
- recursive calls of BFS produces a spanning forest that provides specific
  information about the graph
- can use a global vertex marking vector to avoid visiting duplicate edge vertices
- keep an st vector to record parent links, and an order vector for order visited
- shortest path:
    - use a parent link vector and stop BFS at destination vertex
    - the path from w to v through the parent link vector is the shortest path
    - to get v to w search from w to v or reverse the order using a stack
- single source shortest paths:
    - finds the shortest path from v to all other vertices
    - run BFS to completion and the parent link vector will contain the shortest
      path from each vertex back to the start vertex
- all-pairs shortest paths
    - run and store the result of single source shortest path for each vertex
    - requires time proportional to V * E and space proportional to V^2

## Generalized Graph Search
- both DFS and BFS are examples of generalized graph search algorithm
- fringe - the structure used to store vertices to search
- use an empty tree to store paths
- general steps:
    - add start vertex to fringe
    - move an edge from the fringe to the tree
    - visit the edge if unvisited
    - put all edges on fringe for given vertex that lead to unvisited vertices
    - continue
- fringe -> queue == BFS, fringe -> stack == DFS
- graph search coloring:
    - white -> unvisited
    - black -> in tree/visited
    - gray -> in fringe/yet to be visited

- properties:
    - generalized graph search visits all vertices and edges in V^2 for matrix repr
      and V + E for list repr plus (in the worst case) the time required for V insert,
      V remove, and E update operations in a generalized queue of size V
```
// Program 18.10 Generalized graph search
```
- alternatives to BFS and DFS:
    - randomized queues: randomly remove the next vertex from the fringe so each
      vertex is equally likely to be the next inserted in the tree
    - use a priority queue for the fringe (important for many algorithms) and use
      highest priority as the next inserted into the tree
```
// Program 18.11 Random queue implementation
```
- can generalize graph search further by using a forest as output rather than a tree

## Analysis of Graph Algorithms
- TODO: more work here



# Digraphs and DAGs
- edge ordering becomes significant, i.e. edges have a direction

## Glossary and Rules of the Game
- definitions:
    - digraph: vertices with directed edges that connected ordered pairs of
      vertices with no duplicate edges (directed edges go from first vertex to second
      vertex)
    - directed path: a list of vertices with a directed edge connecting each vertex
    - reachable: a vertex t is reachable from s if there is a directed path from s to t
    - map: a digraph where self loops are allowed
    - indegree: number of directed edges that lead to a vertex
    - outdegree: number of directed edges leading out of a vertex
    - directed acyclic digraph: a directed graph with no cycles
    - strongly connected digraph: a digraph where every vertex is reachable from every
      other vertex
    - strongly connected or mutually reachable: a pair of vertices in a digraph
      that have a directed path from one to the other and vice versa
    - kernel DAG: see the second property below
- reversing a digraph:
    - take an input digraph and an empty output digraph and insert the reversed edge
      to the output digraph
    - for the matrix repr -> can interchange rows and columns or just swap s and t
      when accessing an element
- properties:
    - a digraph that is not strongly connected is a set of strongly connected
      subgraphs with a directed edge between them
    - given a digraph, D, and a digraph K(D) defined such that each vertex
      corresponds to a strongly connected subgraph of D and each edge in K(D)
      corresponding to an edge that connects those components in D, then K(D)
      is a DAG (aka the kernel DAG of D)

- NOTE:
    - connectivity -> used for undirected graphs
    - reachability -> generally reserved for use with digraphs
    - strong connectivity -> not a special property for connected vertices
      in an undirected graph

## Anatomy of DFS in Digraphs
- while the general outline is the same, DFS in digraphs is more complicated
- preorder and postorder become more important here
- same edge labeling as above for DFS trees:
    - tree edges: edges representing a recursive call
    - back edges: edges from a vertex to an ancestor
    - down edges: edges from a vertex to a descendant
    - cross edges: edges between a vertex and another vertex that is neither
      an ancestor nor a descendant

```
// Program 19.2 DFS of a digraph
```

- directed cycle detection:
    - can be achieved in a digraph with DFS by check for back edges
- single source reachability:
    - the set (and count) of reachable vertices from a start give start vertex

- properties:
    - in a DFS forest for a digraph, an edge:
        - to a visited node is a back edge if it leads to a node with a
          higher postorder number
        - is a cross edge if it leads to a node with a lower preorder number
        - is a down edge if it leads to a node with a higher preorder number
    - a digraph is a DAG if and only if we encounter no back edges when we use
      DFS to examine every edge
    - the single-source reachability problem can be solved for a vertex s in a
      digraph in time proportional to the number of edges in the subgraph
      induced by the reachable vertices

## Reachability and Transitive Closure
- TODO: return to this section to take more notes
- definitions:
    - transitive closure: for a digraph, the corresponding digraph with an
      edge from s to t if and only if there is a directed path from s to t in the
      original graph
    - boolean matrix: matrix with binary values

```
// dot product of matrices
for (s = 0; s < num_vertices; s++) {
    for (t = 0; t < num_vertices; t++) {
        for (i = 0; c[s][t] = 0; i < num_vertices; i++) {
            // classic version: c[s][t[ += a[s][i] * b[i][t]
            // c++ version: if (a[s][i] && b[i][t]) c[s][t] = 1;
        }
    }
}

// Warshall's algorithm
for (i = 0; i < num_vertices; i++) {
    for (s = 0; s < num_vertices; s++) {
        for (t = 0; t < num_vertices; t++) {
            if (a[s][i] && a[i][t]) a[s][t] = 1;
        }
    }
}
```
- properties:
    - we can compute the transitive closure of a digraph by constructing the
      by constructing the the graph's adjacency matrix A, adding self-loops for
      every vertex, and computing A ^ V
    - Warshall's algorithm can compute the transitive closure of a digraph in time
      proportional to V ^ 3
    - constant time reachability testing (abstract transitive closure) for a digraph
      using space proportional to V^2 and time proportional to V^3 for preprocessing
    - we can use any transitive-closure algorithm to compute the product of two boolean
      matrices with at most a constan-factor difference in running time
    - with DFS, we can support constant query time for the abstract transitive closure
      of a digraph, with space proportional to V^2 and time proportional to V (E + V) for
      preprocessing (computing the transitive closure)
```
// Program 19.3 Warshall's algorithm
```
```
// Program 19.4 DFS-based transitive closure
```

## Equivalence Relations and Partial Orders
- set theory has a relationship to abstract transitive closure algorithms
- definitions:
    - relation: given a set, a set of ordered pairs of the objects in the set
    - symmetric: a relation R if sRt means tRs
    - reflexive: for a relation sRs
    - transitive: sRt and tRu implies sRu
    - partial order: a transitive relation that is also irreflexive (also asymmetric and acyclic)
    - total order: partial order where sTt or tTs for all s != t (check this definition)

- partial orders are related to:
    - subsets
    - paths in DAGs
- list of corollaries between set theory and graphs:
    - relations <-> digraphs
    - symmetric relations <-> undirected graphs
    - transitive relations <-> paths in graphs
    - equivalence relations <-> paths in undirected graphs
    - partial orders <-> paths in DAGs

## DAGs
- prototypical DAG problems:
    - scheduling, tasks, constraints
- definitions:
    - scheduling: determine an ordering for a set of tasks with constraints
      in the form of a partial ordering
    - binary DAG: DAG with two edges leaving each node, identified as the left
      edge and the right edge, either potentially null
- scheduling is topological sorting
- DAGs are part tree, part graph
- a DAG can be recursively traversed in the same way as a tree
- the distinction between a binary DAG and a binary tree is that a nodes in a
  binary DAG can have more than one path to it
- binary DAGs can sometimes lead to more compact representations of a binary tree
- trie keys can be viewed as a correspondence to rows in a truth table for
  a boolean function for which the function is true -> binary DAG can be viewed as
  an economical circuit that computes the function
```
// Program 19.5 Representing a binary tree with a binary DAG
```

## Topological Sorting
- TODO: take more notes here
- source: a vertex with only outgoing edges
- sink: a vertex with only ingoing edges
- two flavors:
    - relabel: given a DAG, relabel its vertices such that every directed
      edge points from a lower numbered vertex to a higher numbered one
    - rearrange: given a DAG, rearrange its vertices on a horizontal line such
      that all the directed edges point from left to right
- properties:
    - postorder numbering in DFS yields a reverse topological sort for any DAG
    - every DAG has at least one source and at least one sink

```
// Program 19.6 Reverse topological sort
```
- can reverse the DAG before sorting
- can push the index onto a stack
```
// Program 19.7 Topological sort
```
- NOTE: review pigeonhole principal
- a topological sort algorithm follows from the second property above
- NOTE: review the explanation here
```
Program 19.8 Source-queue based topological sort
```