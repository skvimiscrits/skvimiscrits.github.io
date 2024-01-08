---
layout: page
title: Classical problems - 2
parent: DP
math: true
nav_order: 3
---

## Travelling Salesman (Shortest Hamiltonian path)

Given $N$ cities ($1,\ldots,N$) and distances between them,
find the minimum distance to visit them all (if you can start from any).

Let $d(u, v)$ denote distance between cities $u$ and $v$.

**Solution**

A brute force solution is considering all orders of visiting the cities.
But this means we have to consider all $N!$ permutations. This is feasible for
very small $N$, probably less than $10$.

The dynamic programming solution is based on the following function $F$:

Let $F(V, x)$ be the minimum distance to visit all cities in set $V$ with $x$
being the last visited city.

The domain of $F$ has size $2^N \cdot N$ as there are $2^N$ possible sets for $V$ and $N$ cities for $x$.

$$
F(V, x) = \begin{cases}
  0 & V = \{x\} \\
  \min_{u \in V, u \ne x}{\left[d(u, x) + F(V - \{x\}, u)\right]} & \text{otherwise}
  \end{cases}
$$

The overall solution is given by minimum of $F(\{1,\ldots,N\}, i)$ over $i = 1,2,\ldots,N$.

> The problem stated above is basically equivalent to finding the shortest **Hamiltonian path**
> in an undirected weighted graph.
> 
> By adding the condition to return back to the starting point (which will not change
> the complexity of the problem), this will be finding the shortest **Hamiltonian cycle**.
> 
> A **Hamiltonian path** is a path in a graph which visits all vertices exactly once.
> 
> A **Hamiltonian cycle** is similar to Hamiltonian path with only difference being a cycle.

## Existence of Hamiltonian Path

Given an undirected, unweighted graph with set of vertices $V = \{1, \ldots, N\}$ and set of edges $E$, find
(for all pairs of vertices $u, v$) whether there exists a
hamiltonian path starting from $u$ and ending at $v$.

**Solution**

Define $F(S, u, v)$ (boolean function) to denote whether there exists a path from $u$ to $v$
visiting each vertex in set $S$ exactly once (note: $\{u, v\} \subseteq S$).

Then,

$$
F(S, u, v) = \begin{cases}
1 & \text{if } (S = \{u, v\}) \land ((u, v) \in E) \\
\bigvee\limits_{w \in S, w \notin \{u, v\}, (w, v) \in E}{F(S - \{v\}, u, w)}  & \text{Otherwise}
\end{cases}
$$

$F(N, u, v) = 1$ means there exists a Hamiltonian path from $u$ to $v$.

> If we do not care about both ends of the path,
  we would define $F(S, u)$ to denote whether there exists a path
  ending (or starting) at $u$ visiting each vertex in $S$ exactly once.

Extension:
- Finding Hamiltonian Cycle (in weighted graph) maximizing the
  minimum value of edges in cycle.
- Minimizing the maximum value of edges in cycle.
- Same as above but maximizing sum (TSP like?)

## Max Clique

Clique is a complete subgraph of a graph (i.e. in that subgraph, each vertex 
is adjacent to every other vertex)

Max clique is a clique with maximum size among all cliques.

Given a graph of $N$ vertices labelled $1$ to $N$, and set of edges $E$, find
size of a max clique.

**Solution**

By definition, any subgraph of a clique is also a clique.

So, if a set of vertices $V$ form a clique, then $V \cup \{u\}$ forms a clique
if $(u, v) \in E \,\forall\, v \in V$.

Let $F(V)$ be size of max clique that can be formed from set of vertices $V$.
Then,

$$
F(V) = \begin{cases}
1 & \text{if } \vert V \vert = 1 \\
\vert V \vert & \text{if } F(V - \{u\}) = \vert V \vert - 1 \text{ and } (u, v) \in E \;\forall\; v \in V - \{u\} \text{ for some } u \\
\max\limits_{u \in V}(F(V - \{u\})) & \text{otherwise}
\end{cases}
$$

> We can implement this using bitmasks (thus having $2^N$ states) in $O(N \cdot 2^N)$.
> 
> But this will work only for very small $N$ (say $N \le 20$)

We can optimize this approach. Let's divide the graph into $G_1(V_1, E_1)$ and $G_2(V_2, E_2)$. Vertices are divided evenly,
hence $\vert V_1 \vert \approx \vert V_2 \vert \approx \frac{N}{2}$. 

Further, define $g(V)$, for $V \subseteq V_1$, to give set of vertices in $V_2$ such that all vertices in that set are adjacent to all vertices in $V$.
Thus, $g$ is becomes the connecting link between $G_1$ and $G_2$

> $g$ is can be computed in $O(2^{\frac{N}{2}}(\frac{N}{2})^2)$ for all states.

We can compute $F$ defined above for both $G_1$ and $G_2$ independently. This will take
be $O(2^{\frac{N}{2}}N)$ time.

Now, suppose we have a max clique from $V \subseteq V_1$. We want to find whether there
are other vertices involved (from $G_2$) forming a bigger clique. Let $U = g(V)$, then
we can combine the max clique formed by $U$ in $G_2$ to that formed by $V$.

This holds because, by definition of $g$, each vertex in $V$ is adjacent to each vertex in $U$.

> Using optimized approach, we can implement this for relatively bigger $N$ (say $N \le 40$)
>
> We can use bitmasks for convenient implementation
> - For example, bitmask based adjacency list can help in checking whether a
>   set of vertices are adjacent to a vertex in $O(1)$.

***