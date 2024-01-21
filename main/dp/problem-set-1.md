---
layout: page
title: Problem set - 1
parent: DP
math: true
nav_order: 5
---

### Clearing segments

You are given an array $a$ of $n$ integers.
In one move, you can select a contiguous segment (subarray)
such that all values in it are same and clear it (shrinking the original array). Clearing
a segment of length $k$ gives $k^2$ points.

Find the maximum score.

Constraints:
- $1 \le n, a_i \le 200$

#### Ideas

Can we formulate a recursive definition for DP?

Let $f_{i,j}$ be max score for segment $[i, j]$ solved independently.

When element $i$ is cleared in the optimal solution,
the segment must have been of form $[i, x]$ (element $x$ still present).
This means that segments $[i, x]$ and $[x+1, j]$ are cleared
independently and ordering will not matter. This leads
to first candidate of $f_{i,j}$ as $\max\limits_{i \le x \lt j}{(f_{i,x} + f_{x+1,j})}$

What if element $i$ is cleared with $j$ itself, i.e., last
clear is of form $[i, j]$. This case is a bit complicated.
Consider elements $x_1,\ldots,x_m$ in $(i,j]$ having same
value as $a_i$:
- Before clearing $[i, j]$ it is possible that all of $x_i$ are present.
- It is also possible that none remain - all get erased with
  other (internal) segment clears.
- In general, if we assume that some subset of $\{x_1, \ldots, x_m\}$ elements
  remain, then we can
  calculate the overall result but we have to consider $2^m$ subsets.
- If we only consider the first element that will remain, let it be $x$,
  then we can compute result as $f_{i+p,x-1} + f_{x,j} + p^2$ (assuming first $p$ elements have same
  value $a_i$). This is not correct because we could have
  cleared the $p$ elements together with segment $[x, j]$ yielding
  better score.
- This leads to a definition where an arbitrary number of elements,
  of same value as first element,
  are assumed to be present at start of the segment.

Let $f_{i,j,k}$ be the max score for segment $[i, j]$ assuming
$k$ elements of value $a_i$ being present at start of $[i, j]$ (meaning that
the overall length of segment is $j - i + 1 + k$)

Base case is: $f_{i, i, k} = (k + 1)^2$

Also, $f_{i,j,k} = f_{i+1,j,k+1}$ when $i \lt j$ and $a_i = a_{i+1}$.

From now on we will assume that $a_i \neq a_{i+1}$ when $i \lt j$.

General case: $f_{i, j, k}$ is max of:
- $\max(f_{i, x, k} + f_{x + 1, j, 0})$ $\forall$ $i \le x \lt j$ (the independent split case)
- $\max(f_{i+1,x-1,0} + f_{x,j,k+1})$ $\forall$ $(i + 1 \lt x \le j) \land (a_i = a_x)$

The first case is straightforward. For second case, we already
have assumption that $a_i \neq a_{i+1}$. For some $x$ such that $a_i = a_x$
and segment $[i+1,x-1]$ is cleared, $a_i$ will be cleared with $[x, j]$. This explains
the $k+1$ in $f_{x,j,k+1}$, there were $k$ elements already in front and
$1$ element $a_i$ added to segment $[x, j]$.

***