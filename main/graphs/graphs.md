# Graph

A graph is a set of entities (called nodes or vertices) and relations
between them (defined by edges).

## Classification

### By type of relation (edges)

- Unidirectional: Directed graph
- Bidirectional: Undirected graph

### By connectivity

- Connected (in case of undirected graphs):
  If there exists a path from every vertex to every other vertex.

In case of directed graphs, such graph is called **strongly connected**.

### By existence of cycles

- In case of a directed graph: if there are no cycles, then
  the graph is called **Directed Acyclic Graph** (DAG)
  - This has lots of practical applications

- A connected undirected graph with no cycles is a **tree**.


## Properties

**In a general connected undirected graph, there are
always even number of vertices having odd degree**

Proof: By induction on addition of edges

Let the original graph be $G = (V, E)$

Let the current graph be $G' = (V, E')$ formed by adding $k$
edges to $(V, \phi)$ (here $\phi$ is empty set)

Base Case ($k = 0$):
In this case there is no edge, hence each vertex has a degree $0$.
There are no vertices having an odd degree.

Inductive hypothesis : $G'$ has even number of vertices
having odd degree after adding $k$ edges.

Let $T$ denote number of vertices having odd degree. Then
$T = 2p$ (by inductive hypothesis)
On adding $(k + 1)$-th edge between vertices $u$ and $v$, there
can be three cases ($d_x$ denotes degree of vertex $x$):
- Both $d_u$ and $d_v$ are even.
  In this case both will now become odd and will not
  violate the required condition:
  $T = 2p + 2 = 2(p + 1)$
- $d_u, d_v$ have different parity.
  After adding the edge, parity will be swapped but number 
  of odd/even degree vertices won't change.

***