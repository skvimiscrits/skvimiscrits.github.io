# Depth first search (DFS)

DFS is traversal of a graph where we start exploring a
node (further) as soon as it is found.

More precisely:
- We start with some node.
- If some unexplored adjacent node is present, explore that node fully.

### Algorithm

Assume that $S_u$ gives explored status of a node (or vertex).
$S_u = 1$ if $u$ is visited and $0$ otherwise.

Let input graph be $G(V, E)$. Assume that it is undirected
and connected.

Define recursive procedure $\operatorname{dfs}(u)$:
- Process $u$
- $S_u \leftarrow 1$
- For each node $v$ adjacent to $u$:
  - If $S_v = 0$ call $\operatorname{dfs}(v)$

Observe that:
- Each node is processed exactly once.
  - A node is processed at most once as after processing we
    mark it as visited. We never visited already visited nodes.
  - A node is processed at least once as we call this procedure
    recursively on each adjacent unvisited node and the graph is connected.
- Order of processing of nodes depends on:
  - Starting node, i.e., the node on which this procedure is called initially.
  - Order of iterating through adjacent nodes.

## DFS Tree

Suppose we are present at node $u$. For each $v$
we visit, we can mark $v$ as child of $u$.

- No vertex will be marked as child of more than one
  vertex. This is because marking as child and visiting
  take place in conjunction.

The starting vertex will be the root of the tree.

### Uniqueness

A DFS tree is unique for a graph for a fixed
order of visiting the adjacent vertices for
each vertex.

Different DFS trees may be obtained on changing
order of elements in adjacency list.

## Paths

```py
adj = [] # Adjacency list
vis = [False] * N # Visited flag
path = []

dfs(u):
  vis[u] = True
  path.append(u)
  print(path)
  for v in adj[u]:
    if not vis[v]:
      dfs(v)
  path.pop() # removes u
```

The method `dfs` is similar to what we defined earlier.
But the difference is that now we append nodes to current path.
It holds that:
- On start of call `dfs(u)`, `path` contains the
  path from root to node $u$ in the DFS tree of the graph.
- Let size of list `path` be $k$ before a call to `dfs`,
  then size will be $k$ after return of the call.
- For a leaf in DFS tree it holds true, thus
  it is true for all nodes inductively.

### All possible paths

Suppose we want to find all possible simple paths
in a graph starting from a particular vertex.

```py
adj = [] # Adjacency list
vis = [False] * N # Visited flag
path = []

dfs(u):
  vis[u] = True
  path.append(u)
  print(path)
  for v in adj[u]:
    if not vis[v]:
      dfs(v)
  path.pop() # removes u
  vis[u] = False
```

By adding `vis[u] = False`, we mark vertex $u$ unvisited again.

We can state two things in this recursion:
- If we are some node $u$ with path as $[x_1, \ldots, x_k]$ ($x_k = u$)
  then all $x_i$ have visited state marked as true.
  Thus, this path is always a simple path.
- `dfs` reverts the state back to original after
  calling. Hence, if we are at some vertex $u$ and we call
  `dfs` for each adjacent vertex $v$ (not already in path), the
  method is independent of the order in which we call `dfs(v)` as
  after each call, the state will be back to original.

## More analysis

We will now consider directed graphs. For undirected graphs,
most concepts will remain same.

Suppose we are starting DFS from node $r$ in a directed
graph. Let $c_1,\ldots,c_k$ be the nodes adjacent to $r$.

After call $\operatorname{dfs}(c_1)$ finishes. We have:
- If some $c_i$ ($i \gt 1$) is visited, then:
  - $c_i$ is reachable through $c_1$ (without going through $r$)
  - There exists a cycle containing vertices $c_1, c_i, r$
  - $c_i$ will be a part of subtree (of DFS tree) rooted at $c_1$
- If some $c_i$ ($i \gt 1$) is not visited, then:
  - $c_i$ is reachable from $c_1$ only via $r$
  - $c_i$ will be part of a different subtree (of DFS tree) than that
    rooted at $c_1$

Recall the definition of status $S_u$ of node $u$:
- $S_u = 0$ is $u$ is not visited
- $S_u = 1$ if $u$ is visited

We will redefine it:
- $S_u = 0$ if $u$ is not visited
- $S_u = 1$ if $u$ is visited but not fully explored (under exploration)
- $S_u = 2$ if $u$ is visited and fully explored

When we say $u$ is fully explored, we mean that all nodes
in subtree (of DFS tree) rooted at $u$ are fully explored.
- The base case for this recursive definition are leaves which will be
  fully explored without visiting additional nodes.

We will now redefine DFS with these changes:

Define recursive procedure $\operatorname{dfs}(u)$:
- Process $u$
- $S_u \leftarrow 1$
- For each node $v$ adjacent to $u$:
  - If $S_v = 0$ call $\operatorname{dfs}(v)$
- $S_u \leftarrow 2$

For the following discussion, assume that we already know the DFS tree.
This is possible if we use a fixed order of visitation of adjacent vertices.

Observations on subtree (of DFS tree) rooted at $u$:
- A node can be fully explored only when the subtree rooted
  at that node is fully explored. This implies that there can't
  be a node $x$ such that $S_x \neq 2$ and $S_y = 2$ where $y$ is an ancestor
  of $x$.
- If $S_x = 1$ for some node $x$, then $S_y = 1$ for all ancestors $y$ of $x$
- If $S_x = 0$ for some node $x$, then in the ancestor chain
  $y_1,\ldots,y_k$ ($y_k = r$) we will have some $t$ ($1 \le t \lt k$) such that:
  - $S_{y_i} = 0$ for $1 \le i \le t$
  - $S_{y_i} = 1$ for $t \lt i \le k$
- For any two nodes $x$ and $y$ if $S_x = 1$ and $S_y = 1$, then
  either $y$ is ancestor of $x$ or $x$ is ancestor of $y$.
  - Suppose this is not the case. Let $z$ be the lowest common
    ancestor of $x$ and $y$. Since we will always finish visiting
    one child before moving on to another, then either $S_x = 2, S_y = 1$
    or $S_x = 1, S_y = 2$.
- For undirected graphs: It can never be a case that while processing node $u$, there is an edge $(u, v)$
  such that $S_v = 2$ and $S_v$ is *not* a child of $u$ in DFS tree.
  - This is because if $v$ was fully explored, then $u$ should also have been explored.
  - It directly follows that there are *no crossing edges in undirected graph*.

### Categorization of edges

Suppose we are processing node $u$. For some adjacent node $v$,
$S_v$ can give us the following information:
- If $S_v = 1$ then $v$ will be an ancestor of $u$ (see observations above)
  - If $v$ is not the (direct) parent of $u$, then there is a cycle
    formed by path from $v$ to $u$ in tree and the edge $(u, v)$.
    Such an edge is called **back-edge**.
- If $S_v = 0$ then $v$ will be visited and it will be a (direct) child of $u$.
  Such an edge is called **tree-edge** since it is a part of DFS tree.
- If $S_v = 2$ then $v$ is part of some other subtree in the graph (which is fully visited).
  Such an edge is called a **crossing-edge**. In case of undirected graph, this will
  form a cycle.

***