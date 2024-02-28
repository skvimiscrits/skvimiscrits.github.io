# State space graphs

In some cases, the states in a problem can be considered as vertices
and transitions as edges. Then we can reduce the problem to a graph problem.

## Illustration 1

*Source: atcoder abc289_e*

You are given an undirected graph with vertices numbered $1$ to $N$ and $M$ edges.
Each vertex has a color which is either white or black.

We have a game with two pieces $A$ and $B$. Initially, pieces $A$ and $B$
are placed at vertices $1$ and $N$ respectively. One move is: you have to move
piece $A$ to an adjacent vertex $u$ and piece $B$ to an adjacent vertex $v$
where $u$ and $v$ have different colors.

Find minimum number of moves such that piece $A$ is at vertex $N$
and piece $B$ at vertex $1$ (or report if this is not possible).

Constraints:
- $1 \le N, M \le 2000$

**Ideas and solution**

We can define state of this game as $(x, y)$ where $x$
and $y$ are vertices where pieces $A$ and $B$ are placed.

The initial state is $(1, N)$. The desired state is $(N, 1)$.

We can transition from state $(x, y)$ to $(u, v)$ if colors
of $u$ and $v$ are different.

Thus, we have defined the state space graph.
- The number of states (or vertices) is $N^2$
- The number of transitions (or edges) is $M^2$
  - Because we sum up for each $u, v$: $\vert E_u \vert \cdot \vert E_v \vert$
    wher $E_u$ is set of edges adjacent to vertex $u$.

The problem is now simple: find the length shortest path from $(1, N)$
to $(N, 1)$ in an unweighted directed graph. This can be done using BFS.

> Coincidentally, this is a graph problem which has to be first
  converted to state space graph.

***
