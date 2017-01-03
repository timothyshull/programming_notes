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

##