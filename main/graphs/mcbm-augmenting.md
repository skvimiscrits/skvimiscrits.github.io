# Maximum cardinality bipartite matching

In a bipartite graph, a *matching* is a set of pairs
of vertices such that:
- Each pair contains vertices of different color (i.e., in sense of graph being 'colored' into two colors).
- No vertex appears more than once across all pairs.

A *maximum cardinality matching* is the optimal matching in which the
size of *matching* set is maximized.

## Augmenting path algorithm to find MCBM in a bipartite graph

Let the vertices be divided into two sets $A$ and $B$.

**Augmenting path**: A path which starts from an unmatched
vertex $s$ in $A,$ followed by an unmatched edge to a matched
vertex in $B,$ followed by matched edge to a matched vertex in
$A$ and so on until an unmatched vertex in $B$ is reached.

**Berge's Lemma**: It states that a matching $M$ in a graph $G$ is
maximum (contains the largest possible number of edges) if
and only if there is no augmenting path (a path that starts
and ends on free (unmatched) vertices, and alternates
between edges in and not in the matching) with $M$.

Ideas behind this algorithm:
- Augmenting paths are always of odd length (can be proved)
- If there exists an augmenting path of length $2k + 1$ then
it contains $k$ matched edges and $k + 1$ unmatched edges. If we
flip the status of all the edges in this path, we obtain a
better matching (increased by $1$).
- We find and eliminate augmenting paths

Also note:
- Since augmenting paths have odd length, any such path will start
  and end in different sets. Also, the starting set of the path
  does not matter because we can always invert that path. WLOG,
  we can assume that every such path starts from $A$.
- If a vertex is unmatched, none of the adjacent edges will be matched.
-  It a vertex is matched, exactly one of the adjacent edges will be matched.
- An augmenting path will always start and end at unmatched vertices.

### Algorithm

1. Let $s$ be an unmatched vertex in $A$.
1. Let $P = [s]$ (the current augmenting path).
1. Let $u$ be the last vertex $P$ (it will hold that $u \in A$).
1. If there exists an edge $(u, v)$ such that $v$ is unmatched,
  then the augmenting path completes at $v$. Add $v$ to $P$ and return $P$.
1. If there exists an edge $(u, v)$ such that $v$ is *matched* (with say $w$ in $A$),
  then add $v$ and $w$ to $P$ and go to (3).
1. Return 'no augmenting path exists'.

### Implementation 1

This implementation actually traverses the augmenting
path by maintaining status of matched edges and vertices (it
needs to check if an edge is matched of not, or a vertex is
matched or not)

```
# matching(u, v) is 1 if (u, v) is part of matching 0 otherwise
# side is 0 for A and 1 for B
# returns a boolean denoting whether an augmenting path is found

find_aug(u, side)
  # to avoid repitition
  visited[u] = true

  if side == 1 and u is NOT matched
    set u as matched
    return True

  for each v in adj[u] such that visited[v] = false
    # A->B should be unmatched and B->A should be matched
    if matching(u, v) == side 
      res = find_aug(v, 1 - side)
      if res
        invert matching(u, v)
        return True

  return False
```

### Implementation 2

Unlike above this implementation does not actually
traverse the augmenting path. Also it does not need to check
if an edge is matched or not. However, it keeps the matched
vertex for each vertex in $B$. Suppose $v \in B$ is matched to
$u \in A$ then `match[v] = u`.

```
# match[v] == -1 if v is not matched
# u will always be a vertex of A

# if match[v] is u itself, then aug(u) will return false
# since we have already marked v as visited

aug(u)
  if visited[u] return False

  visited[u] = true

  for v in adj[u]
    if match[v] == -1 OR aug(match[v])
      match[v] = u
      return True

  return False
```

***