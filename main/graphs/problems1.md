***

## Min cost arborescence

For a DAG

- Let r be the root. Remove all incoming edges to r

- For each vertex v (!= r) find remove all incoming edges except for the
cheapest one.

- If the resulting graph is an arborescence, then it the min-cost
arborescence (because all cheapest edges selected)
Otherwise => impossible
  - Why? Choose any vertex v (!= r) and traceback to the vertex from which the
  edge is incoming. You will eventually reach r because there are no cycles and
  each vertex has exactly 1 incoming edge (Except for r, which has 0)

***

## Max Clique Problem

- for small graphs (n <= 40)

For a graph G = (V, E), a clique is a subset U of vertices V such that for
each pair of vertices in U there is an edge between them. In other words, a
clique is a subgraph of G which is completely connected.

Max clique is a subset (clique) U of V such that there does not exist any
other subset (clique) U’ of V such that |U| < |U’|

For very small graphs, n <= 20 (|V| = n), we can iterate through each subset
and check complete connectivity. Assuming adjacency lists in form of bitmask,
this can be done in O(n * 2^n)

For relatively large graphs, n <= 40, we can use the following technique:
First split the vertices into two sets P and Q (abs(|P| - |Q|) <= 1)
(|P|,|Q| ~ n/2, approximately)
Iterate through all subsets of P and check for cliques.

Let A[x] = 1 ; vertices in mask x (which belong to P) form a clique
           0 ; otherwise

Similarly, for Q:
Let B[x] = 1 ; vertices in mask x (which belong to Q) form a clique
           0 ; otherwise

There can be three cases: A max clique is formed from vertices
only in P
only in Q
in both P and Q

Case 1 and 2 are covered by A & B defined above. We will analyse case 3.
Consider a mask x (vertices from P) such that A[x] = 1
Let u_1, u_2, …, u_k be the vertices present in x
Then these vertices form a clique. Also,
If adj[u_1] & adj[u_2] & … & adj[u_k] = m
Then, m & x = x
Why not m = x? Because there might be vertices v_1, v_2, …, v_l from set Q present in m. Let’s denote this set of vertices by y.
Then, x | y = m
Basically x = {u_1, …, u_k}
          y = {v_1, …, v_k}
          m = Union(x, y)
Observe that each of the vertices in y are adjacent to each of u_1, …, u_k. But are they adjacent to each other?

We need the answer to: What is the largest subset of y which is a clique. We need the largest subset y’ of y such that B[y’] = 1. Then we have a clique with vertices from both sets, this clique has size: |x| + |y’|.

This is a standard problem of max/min over subsets:
Let f(S) be some function defined for each subset S of {1...n}
Let g(S) = max{f(T) | T is a subset of S}
Then,
g(S) = max{g(S - {i}) | i belongs to S}
Assuming we have calculated g for subsets of size K, we can compute g for subsets of size K+1.
Thus, g can be calculated in n * 2^n.
This technique is called Sum over Subsets (SOS) DP

Note that an independent set is the same as clique if all edges are inverted. So, we can also solve the max independent set problem using the same technique.

Implementation:
[Cliquer](https://github.com/terxor/cp-common/blob/master/impl/graphs/cliquer.cpp)

Some problems:
- https://codeforces.com/contest/1105/problem/E

***