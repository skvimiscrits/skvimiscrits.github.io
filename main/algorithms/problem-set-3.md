---
layout: page
title: Problem set - 3
parent: Algorithms
math: true
nav_order: 13
---

## Minimum adjacent swaps to sort

You are given a permutation $p_1, \ldots, p_n$ of $1$ to $n$.
Find minimum number of adjacent swaps (that is, $p_i, p_{i+1}$) to sort the permutation.

**Solution**

Observe that:
- In the final state, no inversions (indices $i, j$ such that $i \lt j$ and $p_i \gt p_j$).
- If we swap $p_i, p_{i+1}$ there are two cases:
  - $p_i \lt p_{i+1}$: number of inversions increases by $1$ after swap.
  - $p_i \gt p_{i+1}$: number of inversions descreases by $1$ after swap.

Thus, it is clear that we will always decrease the number of inversions.
Until the final state is reached, we will always have some $i$ such that $p_i \gt p_{i+1}$.

Mininum number of operations is number of inversions in $p$.

We can use bubble sort algorithm to simulate the process in $O(n^2)$:

- For $j = n$ down to $2$ do:
  - For $i = 1$ to $j - 1$ do:
    - If $a_i \gt a_{i+1}$ do $\operatorname{swap}(a_i, a_{i+1})$

## Minimum swaps to sort

You are given a permutation $p_1, \ldots, p_n$ of $1$ to $n$.
Find minimum number of swaps to sort the permutation.

**Solution**

Define $f(i) = p_i$ then $f$ has cycles when iterated.
Further, every $i$ is part of an independent cycle since $p$ is a permutation.

For example: $p = [1, 3, 4, 2, 6, 5]$ has 3 cycles:
- $1 \rightarrow 1$
- $2 \rightarrow 3 \rightarrow 4 \rightarrow 2$
- $5 \rightarrow 6 \rightarrow 5$

In the final state we want $n$ cycles (each cycle will be $i \rightarrow i$)

Let's observe the effect of swapping $p_i, p_j$ on cycles:
- If $i, j$ (and thus $p_i, p_j$) are part of same cycle,
  the cycle is split into two smaller cycles.
  - If we have $i \rightarrow p_i \rightarrow \ldots \rightarrow j \rightarrow p_j \rightarrow \ldots \rightarrow i$
    then notice how swapping where $i, j$ point to, we get:
    - $i \rightarrow p_j \rightarrow \ldots \rightarrow i$
    - $p_i \rightarrow \ldots \rightarrow j \rightarrow p_i$
- If $i, j$ are part of different cycle, the we get a combined cycle.
  - If we have $i \rightarrow p_i \rightarrow \ldots \rightarrow i$
    and $j \rightarrow p_j \rightarrow \ldots \rightarrow j$
    then notice how swapping where $i, j$ point to, we get:
    - $i \rightarrow p_j \rightarrow \ldots \rightarrow j \rightarrow p_i \rightarrow \ldots \rightarrow i$

Thus, we have two types of operations:
- Same cycle swap: decreases the number of cycles by one
- Different cycle swap: increases the number of cycles by one

It is obvious that we will only perform operation of first type.
If there are $k$ cycles in the initial state, we will require $n - k$ operations.

Now, we can choose any operation which satisfies the above condition.
A simple choice is always swapping $p_i, p_j$ where $p_j = i$ (obviously $p_i \neq i$).

A simple algorithm:
- Let $r \leftarrow 0$
- For $i = 1$ to $n$ do:
  - If $i = p_i$ then skip this iteration
  - Find $j$ such that $p_j = i$
  - $\operatorname{swap}(p_i, p_j)$
  - $r \leftarrow r + 1$
- Output $r$

***