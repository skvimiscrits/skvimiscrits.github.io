---
layout: page
title: DP basics
parent: DP
math: true
nav_order: 1
---

## Dynamic programming - basics

Dynamic programming (DP in short) is involved with
re-using computed results for some function.

DP is mostly used in cases when there is a recurrence relation or DAG involved.

TODO: Add basic examples and techniques

### Illustrations

#### Illustration 1: Maximize with no adjacent elements

You are given integer list $A_1,\ldots,A_N$.
You can select any subset of these with the constraint that if
$A_i$ is selected, then $A_{i-1}$ or $A_{i+1}$ (if present in list) can not be selected.

Find the maximum sum of selected elements.

Constraints:
- $1 \le N \le 10^6$
- $1 \le A_i \le 10^9$

**Solution**

For some $A_i$, selection of only elements $A_{i-1}$ and $A_{i+1}$ impacts whether it can be
selected. This gives us a hint that we might be able to break the problem down to subproblems of smaller size.

Subproblem: Find optimal solution for $A_1,\ldots,A_i$.

Let $f(i)$ be the optimal solution for $A_1,\ldots,A_i$. Then:

$$
f(i) =  \begin{cases}
        0 & \text{if $i = 0$} \\
        A_i & \text{if $i = 1$} \\ 
        \max{(f(i-1), A_i + f(i-2))} & \text{otherwise} \\ 
        \end{cases}
$$

The base case for $i = 0$ is just for convenience of definition.
The base case for $i = 1$ is the solution for smallest subproblem.

The recursive definition part reflects two choices:
- Ignore $A_i$, thus solution is same as $f(i-1)$
- Select $A_i$, then $A_{i-1}$ can not be selected. We use optimal solution
  for $A_1,\ldots,A_{i-2}$ as it doesn't impact $A_i$.

The final answer will be $f(n)$.

***