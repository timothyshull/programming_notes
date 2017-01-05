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


