## Euler tour

A Euler Tour of a graph is a cycle in which
**each edge is visited exactly once**.

### Properties

- *For a graph to have a Euler Tour, each vertex must
  have an even degree*
  - In short, for each edge through which we enter some vertex, there must be
    a different edge to exit it (vice-versa for starting vertex).

- *If we start from some vertex $v$ and keep traversing
unmarked edges (and mark them) it is guaranteed that we will
return to $v$*
  - This is because for each vertex there will be a pair
    of entry and exit edge.
  - This property does not guarantee that all the edges
    of the graph are traversed in the cycle.

### Algorithm

We assume that the input graph contains a Euler tour.

Roughly speaking, the algorithm works in this way:

- Start from some vertex $s$. Keep traversing unmarked edges
(and mark them) until you return to $s$. Let the circuit thus
obtained be $C$.

- In $C$, find a vertex which is adjacent to an unmarked edge.
  If no such vertex exists, there are two possibilities:
  - Each edge has been traversed and C is the Euler Tour
  - The graph is not connected

  Otherwise, let that vertex be $v$. From $v$, recursively
  find a circuit $C'$ (recursively because now we set $v$ as
  starting vertex as done in first step)

  Then, integrate $C'$ into $C$ and repeat this whole step.

We now describe a recursive method $\operatorname{ET}$
which builds the complete Euler tour.
Let the current circuit be $C$ (initially empty)
If we are at some vertex $u$  such that $u$ is adjacent to an unmarked
edge, and $i$ is the position where $u$ should be inserted in
$C$, then $\operatorname{ET}(i, u)$ finds a cycle starting from $u$
and inserts it before $i$.

```
ET(i, u)
  for each v in Adj[u]
    if (u, v) is unmarked
      1. mark (u, v)
      2. j = position of u after inserting before position i
      3. ET(j, v)
```

***