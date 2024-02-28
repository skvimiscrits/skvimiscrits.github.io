## Trees: basics

#### Definition

A tree is a connected undirected graph without cycles
or multiple edges.

A *rooted* tree has a vertex designated as root. Properties:
- It is a directed graph.
- Parent child relationships can be defined. Usually,
  edges are directed from parent to child.
- Root is a node with no parent. All nodes adjacent
  to root are its children. Further parent-child relationships
  can be defined by removing root and recursively considering
  each obtained subtree.

#### Properties

- A tree with $N$ vertices has $N-1$ edges.
  - Start with a single vertex. As you keep on adding vertices, you will add
    exactly one edge every time.

- There is a unique path between each pair of vertices
  - Suppose there are multiple paths, then it would imply that
    there is a cycle.

#### Height

Height of a rooted tree is defined as the longest root to leaf path.

#### Bipartiteness

A tree is a bipartite graph.

*Proof*: We will color nodes black or white. Suppose you color the root as white.
Then, for each level you go down, color nodes in an alternating
pattern. Now, the two colors form two sets of nodes.

*Illustration 1*

You are given a tree with vertices numbered $1$ to $n$.
For each query of form $u, v$ you have to answer the 
parity of distance from $u$ to $v$ (that is, whether even or odd)

*Solution*

Since tree is a bipartite graph, nodes belonging to
same set will have even distance between every pair.

A pair of nodes belonging to different sets will have
odd distance.


