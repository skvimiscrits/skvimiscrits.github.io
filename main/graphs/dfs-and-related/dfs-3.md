# Depth First Search (3)

## Strongly connected components (SCCs)

*Definition*: In a directed graph, a strongly connected component
is a maximal set of vertices such there is a path between every pair of vertices.

### Relation with DFS tree

Suppose we perform DFS on a directed graph and obtain the DFS tree.
We will do further analysis on this DFS tree.

We will now state some important properties:

**Property 1** *Any strongly connected component is a subtree
of DFS tree, with subtrees rooted at some (possibly none) descendants omitted.*

This property can be stated more precisely by the following:
For any two nodes $u$ and $v$ in the SCC, their lowest
common ancestor in DFS tree will also be a part of same SCC.

Suppose $u$ and $v$ have a common ancestor $w$ such that $w \neq u, v$,
then, briefly speaking, the path from $u$ to $v$ will either contain a crossing edge
or pass through $w$. Similarly the path from $v$ to $u$ will either
contain a crossing edge or pass through $w$.
Let $u', v'$ be children of $w$ whose descendants are $u, v$
respectively.
If both paths described above do
not pass from $w$, this means that there will be a crossing
edge from $u'$ to $v'$ and at least one crossing edge
from $v'$ to $u'$. This is not possible.

Thus we can associate root of SCC as the node which
is the lowest common ancestor of all nodes in SCC.

**Property 2** *For some SCC with root $u$, it can not have a
vertex $v$ such that some ancestor $z$ of $u$ is reachable from $v$*

This can be proved simply:
- $v$ is reachable from $z$ since $v$ is a descendant.
- $z$ is also reachable from $v$
- Thus, $z$ and $v$ will be in same SCC.
- But then $u$ can not be the SCC root creating a
  contradiction.

In fact this will hold for whole subtree rooted at $u$
(not only SCC rooted at $u$)

**Property 3** *If we compress all nodes in a SCC (while retaining all edges), a DAG (directed acyclic graph) will
be obtained*

This can be proved by contradiction: suppose there is a cycle,
then all SCCs in that cycle together form a SCC which is not the case.

### Tarjan's algorithm

Tarjan's algorithm works on these concepts:
- From property 2, it follows that node $u$ is a root of
  SCC if no ancestor of $u$ is reachable from
  subtree rooted at $u$.
- For some node $u$, suppose we traverse subtree rooted at $u$
  to take out SCCs rooted at descendants of $u$ (recursively),
  and we end up with a set of remaining vertices $V$ in subtree $u$, then:
  - If no ancestor of $u$ is reachable through some $v \in V$, then $u$ is
    a SCC root. Vertices in $V$ will form SCC rooted at $u$.
  - Otherwise $u$ is not SCC root and we return.

We will use a stack $S$ to keep vertices.
- When we reach a node $u$, we push $u$ to stack $S$.
- When we recurse back from DFS for all children of node $u$,
  then $S$ will contain all vertices in subtree rooted at $u$
  (above $u$ in stack) except for those taken out as SCCs.
- If there is no ancestor of $u$ reachable through its subtree,
  then we will take out all vertices in stack upto $u$ and form
  a SCC.

We will see how stack will help in determining reachability
to ancestor.

**Stack properties**:
- If a node $u$ is present in stack, all ancestors of $u$ will be present
  in the stack.
- If a node is fully explored and still present in stack,
  then it has reachability to some ancestor (otherwise it
  would be a root of an SCC and fully exploring will take
  it out of stack)

For some node $u$, it will have reachability to ancestor if:
- There is a back edge to an ancestor in its subtree
- There is a crossing edge $(v, w)$ and $w$ is in the stack.
  - Note that $w$ is fully explored and it is in the stack.
    Thus, $w$ has reachability to some ancestor, say $z$.
    If $z$ is also fully explored, then we can apply the same
    argument until we reach some node which is under exploration.
    This node will be an ancestor of $u$.

For the back edge check, we can use sequence numbers denoting
order visiting of vertices. Let $P_u$ be sequence number of $u$.
Further, let $M_u$ denote the minimum sequence number reachable from
a back edge in via some node in subtree rooted at $u$. Then, if $M_u \lt P_u$,
then there is a back edge to an ancestor of $u$.

In fact we can use same logic for crossing edges too, except for
those which point to some vertex not present in stack. One solution
is to set $P_u \leftarrow \infty$ for every vertex taken out as
part of SCC.

**Implementation**

Sample code in C++:

```cpp
/**
 * Expected adjacency list adj with vertices numbered 0 to n-1
 * Returns a list of K lists of vertices which are part of SCCs
 * Each list has at least one element and starts with the root of SCC
 **/
vector<vector<int>> scc (const vector<vector<int>>& adj) {
  int n = int(adj.size()), cur = 0;
  vector<int> vis(n, 0), seq(n), st;
  vector<vector<int>> res;
  function<int(int)> dfs = [&] (int u) {
    vis[u] = 1;
    seq[u] = cur++;
    st.push_back(u);
    int low = seq[u];
    for (int v : adj[u]) {
      if (vis[v] == 1) low = min(low, seq[v]);
      else if (vis[v] == 0) {
        low = min(low, dfs(v));
      }
    }
    if (low == seq[u]) {
      vector<int> c;
      while (c.empty() || c.back() != u) {
        int v = st.back(); st.pop_back();
        seq[v] = n + 1;
        c.push_back(v);
      }
      reverse(c.begin(), c.end());
      res.push_back(c);
    }
    return low;
  };
  for (int i = 0; i < n; i++) if (!vis[i]) dfs(i);
  return res;
}
```

Notes:
- `st` represents the stack.
- `dfs(u)` returns the lowest sequence number reachable from
  nodes in subtree rooted at `u`
- While taking out vertices as SCCs, we set `seq[u] = n + 1`
  so that these can't impact `low = min(low, seq[u])`
  checks which are applicable only for back edges and crossing
  edges with nodes in stack.

### SCC DAG

Strongly connected components form a DAG as stated
in a property previously.

For some edge $(u, v)$ in original graph:
- If $u$ and $v$ are part of same SCC, do not add an edge.
- Add an edge from SCC of which $u$ is a part to SCC of which
  $v$ is a part.

Implementation wise, we can mark SCC roots for each vertex.
Further, if there are $k$ roots, we can give rename each
root as $1, \ldots, k$. Thus, we will return new DAG with
vertices numbered $1$ to $k$.

```cpp
/**
 * Expects scc_list as obtained by scc method
 * and original adjacency list
 * Returns a new graph with vertices numbered 0 to k-1
 * where k is number of SCCs.
 * Roots are numbered 0 to k-1 based on scc_list
 */
vector<vector<int>> scc_dag (const vector<vector<int>>& scc_list, const vector<vector<int>>& adj_orig) {
  int n = adj_orig.size(), k = scc_list.size();
  vector<int> root(n);
  unordered_map<int, int> root_id;
  for (int i = 0; i < k; i++) {
    int r = scc_list[i][0];
    root_id[r] = i;
    for (auto v : scc_list[i]) root[v] = r;
  }
  vector<vector<int>> adj(k);
  for (int u = 0; u < n; u++) {
    int u1 = root[u];
    for (int v : adj_orig[u]) {
      int v1 = root[v];
      if (u1 == v1) continue;
      adj[root_id[u1]].push_back(root_id[v1]);
    }
  }
  return adj;
}
```

## SCC Classic problems

### Reachability

Given a directed graph, find whether there exists
a node from which every other vertex is reachable.

**Solution**

We will not use trivial solution to try each vertex
as it would cost $O(n^2)$ time.

Let $x$ be the vertex from which every other vertex
is reachable.

Suppose we have a cycle with vertices $v_1, \ldots, v_k$.
Then if we can reach some $v_i$ from $x$, then we can reach
each of $v_1,\ldots,v_k$ from $x$.

This naturally leads to this observation: If we can reach
a vertex $v$ of some strongly connected component $V$ from $x$, then
we can reach any other vertex of $V$ from $x$.

We can further simply the statement: Given a DAG, check
if there exists a vertex from which every other vertex is
reachable.

This simply requires:
- There should be **exactly one source** (vertex with no incoming edges)
  - This is because if there are multiple sources, you can't reach one
    source from other.
- All vertices should be reachable from source.

We can reduce the given graph into SCC DAG. The source SCC,
if satisfying the conditions, will contain all such vertices
asked for.

Sample implementation for DAG reachability:
```cpp
/**
 * Expects adjacency list adj with vertices numbered 0 to n-1
 * Graph should be a DAG
 * Returns the source vertex of DAG from which all vertices are reachable
 * Returns -1 if no such vertex exists (multiple sources / not all reachable)
 **/
int dag_source (const vector<vector<int>>& adj) {
  int n = adj.size();
  vector<int> inc(n, 0), vis(n, 0);
  for (int i = 0; i < n; i++) {
    for (int v : adj[i]) inc[v]++;
  }
  function<int(int)> dfs = [&] (int u) {
    int cnt = 1;
    vis[u] = 1;
    for (int v : adj[u]) if (!vis[v]) cnt += dfs(v);
    return cnt;
  };
  for (int i = 0; i < n; i++) if (inc[i] == 0) {
    return dfs(i) == n ? i : -1;
  }
  return -1;
}
```

If asked for vertex which **is reachable from** every other
vertex, we can simply transpose the SCC graph and return
answer in the same way.

Transpose is simple:
```cpp
vector<vector<int>> transpose (vector<vector<int>>& adj) {
  int n = adj.size();
  vector<vector<int>> adj1(n);
  for (int u = 0; u < n; u++) {
    for (int v : adj[u]) adj1[v].push_back(u);
  }
  return adj1;
}
```
