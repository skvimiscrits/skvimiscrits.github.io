# Depth First Search (2)

## Articulation points

*Definition*: In an undirected connected graph, a vertex is an articulation point if upon removing it (and the corresponding edges)
the graph  becomes disconnected.

> A single vertex is an not an articulation point because
> an empty is graph is connected (vacuous truth)

The trivial way is actually removing each vertex
and checking connectivity. This will take $O(n^2)$ if there are $n$ vertices.

### Using DFS
We can find articulation points faster as part of DFS.

As in earlier discussion, we will use $S_u$ to denote
status of a node (and consequently check type of edges)
- $S_u = 0$ implies $u$ is unvisited
- $S_u = 1$ implies $u$ is visited but not yet fully explored
- $S_u = 2$ implies $u$ is visited and fully explored

> Here we will assume that we know the DFS tree
> and type of each edge (crossing, tree, back).
> This is a valid assumption because if we do not change
> the order of vertices in adjacency list, we will always
> get the same DFS tree.

Suppose we are at a node $u$ (and $u$ is not the root).
We will analyse this as:
- Subtree rooted at $u$ (in DFS tree)
  - Let the children of $u$ be $c_1,\ldots,c_k$
- Rest of the graph $G'$ (which is a connected **non-empty** graph)

When $u$ is removed from the graph, we will have:
- $k$ subtrees rooted at $c_1,\ldots,c_k$
- $G'$

Non-root node $u$ is an articulation point if there is at least one $c_i$ which is not connected to $G'$.
Otherwise every child $c_i$ is connected to $G'$ and removing $u$ will not disconnect the graph.
- $c_i$ will be connected to $G'$ (after removing $u$) if there is at least one
  back edge from a node in subtree rooted at $c_i$ to some node in $G'$.
- Note that there are no crossing edges in an undirected graph. Thus,
  there will be no edge between some node in subtree rooted at $c_i$ and $c_j$
  ($i \neq j$)

To be able to test existence of back edge in subtree,
we will keep the sequence number in which a particular node
is visited (first node visited will have $1$ and last one will have $n$).
Let $P_u$ denote sequence number of node $u$.

If we are at some node $u$ and find a back edge $(u, v)$
then it will hold that $P_v \lt P_u$.

Let $M_u$ be the minimum of $\min(P_x, P_y)$ over all edges $(x, y)$ in subtree of $u$.
If there is a child $v$ of $u$ such that $M_v \ge P_u$, this
implies that there is no back edge from any node in subtree rooted at $v$.
In that case $u$ is an articulation point.
- But it should hold that there is some ancestor to have a back edge on.
  Thus, $u$ should not be a root.

Thus, for non-root node $u$, if $M_{c_i} \ge P_u$ for some child $c_i$ of $u$, then
$u$ is an articulation point. Otherwise, it is not.

> Leaves are not articulation points by this definition (no children, vacuously true)

Root is a special case because there will be no 'rest of the graph' when
we remove root $r$.
- If there is more than one child, then root is an articulation point.
- The reason for existence of more than one child in DFS tree is that
  no vertex of other children were reachable from first child except via root.

### Implementation

Root has to be handled by case work (keeping number
of children and then marking explicity).

Sample code:

```cpp
/* assumes vertices numbered 0...n-1 */
/* return vector in which i-th value is 1/0 depending on whether
 * vertex i is articulation point or not */
vector<int> articulation_points (vector<vector<int>>& adj) {
  int n = (int)adj.size(), seq, rc;
  vector<int> vis(n, 0), f(n, 0), ap(n, 0);
  function<int(int, int)> dfs = [&] (int u, int p) {
    f[u] = ++seq;
    vis[u] = 1;
    int mn = f[u];
    for (auto v : adj[u]) {
      if (vis[v] == 0) {
        int x = dfs(v, u);
        if (x >= f[u]) ap[u] = 1;
        mn = min(mn, x);
        if (p == -1) rc++;
      } else if (v != p) {
        mn = min(mn, f[v]);
      }
    };
    return mn;
  };
  for (int i = 0; i < n; i++) if (!vis[i]) {
    seq = rc = 0;
    dfs(i, -1);
    ap[i] = (rc > 1);
  }
  return ap;
}
```

Notes:
- `f[u]` is sequence number $P_u$ as defined above.
- `dfs(u, p)` returns $M_u$ for a node `u` having parent `p`.
  This value is `mn` in the function.

## Bridges

*Definition*: In an undirected connected graph, an edge is a bridge
if upon removing it the graph becomes disconnected.

We can find bridges using DFS. Bridges are similar to
articulation points.

Suppose we are at some node $u$ in the DFS tree of the given graph.
Further suppose $u$ has a child $v$. Let rest of the graph (excluding $u$ and
its children subtrees) be $G'$.
Then:

*Edge $(u, v)$ is a bridge if there is no back edge in subtree rooted at $v$
to $u$ or $G'$*

We can use the same concept as that in articulation points: we
can maintain sequence numbers of visiting for each node $u$ as $P_u$
and minimum sequence number reachable from a tree rooted at $u$ as $M_u$.

Then, edge $(u, v)$ is a bridge if $v$ is a child of $u$ and
$M_v \gt P_u$.

### Implementation

There is no edge case for roots.

Sample C++ code:

```cpp
/* assumes vertices numbered 0...n-1 */
/* return vector of edges which are bridges */
vector<pair<int, int>> bridges (vector<vector<int>>& adj) {
  int n = (int)adj.size(), seq;
  vector<int> vis(n, 0), f(n, 0);
  vector<pair<int, int>> res;
  function<int(int, int)> dfs = [&] (int u, int p) {
    f[u] = ++seq;
    vis[u] = 1;
    int mn = f[u];
    for (auto v : adj[u]) {
      if (vis[v] == 0) {
        int x = dfs(v, u);
        if (x > f[u]) res.push_back({u, v});
        mn = min(mn, x);
      } else if (v != p) {
        mn = min(mn, f[v]);
      }
    };
    return mn;
  };
  for (int i = 0; i < n; i++) if (!vis[i]) {
    seq = 0;
    dfs(i, -1);
  }
  return res;
}
```

***