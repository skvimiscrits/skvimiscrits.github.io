---
layout: page
title: Combinatorics - 1
parent: Math
math: true
nav_order: 7
---

## Balls and fences

*How many ways of dividing $n$ balls by using $k$ fences are there?*

Find the number of ways in which $n$ identical objects can be
divided into $k$ groups (each group can be empty).

> Here $k$ groups mean having $k-1$ fences

Suppose we have $n+k-1$ slots in a line. We select $k-1$ of them and
place the fences. In the remaining $n$ slots, we place the balls.
Thus we get $n$ objects divided into $k$ groups.

The number of ways to do this is:

$$
\binom{n+k-1}{k-1} = \binom{n+k-1}{n}
$$

Suppose we want the number of ways to divide $n$ objects into $k$ groups
**where each group has at least $x$ objects**.

Then, it must hold that $n \ge kx$ otherwise there will be no way.

We first divide $n - kx$ objects into $k$ groups and then for each group,
add $x$ objects.

Number of ways of doing so:
$$
\binom{(n-kx)+k-1}{k-1}
$$

## Number of right-down paths in a grid

Given a $N \times M$ grid, find number of paths from
$(1,1)$ to $(N,M)$ if you can only move down or right

There is a simple solution:
$$
f(i, j) = f(i - 1, j) + f(i, j - 1)
$$

with base cases:
- $f(1, j) = 1$
- $f(i, 1) = 1$

But does there exist a closed form for $f(i,j)$?

## Derangements

Find the number of ways to place $n$ objects numbered $1$ to $n$
in $n$ slots also numbered $1$ to $n$ such that object in slot $i$
is not $i$.

Let it be $D_n$.

We can place all except object $1$ in slot $1$. Hence
it can have $n-1$ possible objects ($2,\ldots,n$).

Suppose we chose some object $x (x \neq 1)$ for slot $1$.
We now have $n-1$ slots and $n-1$ objects.

Assume, for now,
that object $1$ has label $x$ (actual $x$ is in slot $1$).
We have a smaller subproblem.
Number of derangements is $D_{n-1}$.

But due to our assumption, we counted some cases in which slot $x$
received $x$ as not being derangements (when in fact object $x$
is actually object $1$). Number of these cases is number
of ways the remaining $n-2$ objects form a derangement.

Thus,
$$
D_n = (n-1)(D_{n-1} + D_{n-2})
$$

Base cases:
- $D_0 = 1$ (Vacuous truth)
- $D_1 = 0$ (Single object, single slot)

Another recurrence relation can be derived for $D_n$ using
this logic: Out of all permutations of $n$ objects,
discard ones where $1, 2, \ldots, n$ objects are placed
in their correct slots.

$$
D_n = (n!) - \left( \binom{n}{1} \cdot D_{n-1} + \ldots + \binom{n}{n} \cdot D_0 \right)
$$

D_n is also called **subfactorial** of $n$


## Forming numbers from digits

You are given $N$ digits $d_1,\ldots,d_N$ ($0 \le d_i \le 9$).


Find the number of integers $\le M$ that can be formed using all of these
digits.

Leading zeroes are not allowed.

## Number of labelled spanning trees in a complete graph

**Cayley's theorem** gives number of trees that
can be formed from vertices labelled $1,\ldots,n$
as $n^{n-2}$.

This also gives the number of labelled spanning trees of $K_n$.

Proof not discussed here.

## Non-intersecting spanning trees in a complete graph

Find the maximum number of spanning trees of $K_n$
that can be formed in such a way that no two have a common edge.

Notes:
- $n$ is **even**
- $K_n$ is complete graph of $n$ vertices.

Let $f_n$ be the required number.

Each edge can be used in atmost 1 spanning tree. We have:
- Number of edges in a spanning tree as $n - 1$
- Number of edges in $K_n$ as $\frac{n(n - 1)}{2}$ or $(n-1) \cdot \frac{n}{2}$

This gives us $f_n \le \frac{n}{2}$.

We will show how to get $\frac{n}{2}$ spanning trees by induction.

Base case: For $n = 2$, we have two vertices and the only
spanning tree we can get is by an edge between them.
Thus, $f_2 = 1$.

Inductive step: Assume $f_n = \frac{n}{2}$ for some $n$ ($n \ge 2$ and $n$ is even).
We will show $f_{n+2} = \frac{n+2}{2}$.

We start from $G = K_n$:
- Add two vertices $u$ and $v$ to graph $G$.
- Add $2n$ edges to graph: $(u, i)$ and $(v, i)$ for $i = 1,\ldots,n$
- Add an edge between $u$ and $v$.
- $G$ now becomes $K_{n+2}$.

We will first obtain $f_n = \frac{n}{2}$ spanning trees in the following way:
- Add edges $(u, i)$ and $(v, n - i)$ to $i$-th spanning tree of $K_n$
  (there will be $\frac{n}{2}$ such trees, so $i = 1,\ldots,\frac{n}{2}$).

Now we have unused edges:
- $(u, v)$
- $(u, i), (v, n - i)$ for $i = (\frac{n}{2}+1), \ldots,n$

We can create another spanning tree using these $n+1$ edges (note that
this graph has $n+2$ vertices).

Thus total number of spanning trees becomes $f_{n+2} = \frac{n+2}{2}$

$\blacksquare$

***