---
layout: page
title: Common techniques - 1
parent: DP
math: true
nav_order: 4
---

## Subsets / bitmask DP

Suppose we want to compute function $f$ for all
$2^n$ subsets of some $n$ elements.

Suppose computation of $f(X)$ depends on subsets of $X$.
For example if the function is maximum, then:

$$
f(X) = \max\limits_{Y \subset X}(f(Y))
$$

What is the time complexity in this approach?
We are computing all subsets for each $X$.
Let $c_x$ denote count of bits in $x$, then total
number of operations (assuming base cases are $O(1)$)

$$
T(n) = \sum_{0 \le x \lt 2^n}{2^{c_x}}
$$

This can be written in an alternate form. Number of
bitmasks having $k$ bits is $\binom{n}{k}$. So,

$$
T(n) = \sum_{0 \le i \le n}{\binom{n}{i} 2^i} \\
T(n) = (1 + 2)^n \\
T(n) = 3^n
$$

## Illustrations

### Illustration 1

Find if it is possible to partition (not necessarily contiguous) integers  $a_1, \ldots, a_n$ into
$k$ sets such that each set has equal sum

Constraints:
- $1 \le k \le n \le 16$
- $1 \le a_i \le 10^9$

**Ideas and solution**

Let total sum be $T$, then obvious we will have a solution only if $k \vert T$
(divides). Let $s = \frac{T}{k}$ be the sum of each partition.

If we can extract out subsets having sum $s$, and end up with an empty set,
then we have a solution. Note that we do not need to track how many
times we have extracted such subsets as it will be fixed ($a_i \gt 0$).
This hints a recursive definition.

Let $f(X)$ denote whether it is possible to partition $X$ into subsets having sum $s$.
For each subset $Y$ of $X$ having sum $s$, we will need to recursively check
$f(X - Y)$. Base case will be empty set $f(\phi) = 1$ (always possible).

Implementation can be done using bitmasks. Since we have to iterate
over all sub-masks, time complexity is $O(3^n)$ (given that we have
pre-computed sub-mask sums).

A more efficient way is possible.
For each set $X$, we will need to know whether it is possible to partition it:
- Into zero or more subsets having sum $s$
- At most one subset having sum $r$ ($0 \lt r \lt s$)

This leads to definition of function $g$. $g(X)$ equals:
- $0$ if it is possible to partition $X$ into subsets having sum $s$
- $r$ if it is possible to partition $X$ into all but one subset having sum $s$. That one subset
  has sum $r$ as defined above.
- $-1$ if none of the above are possible.

Base case is $g(\phi) = 0$.

To compute $g(X)$, we need to check whether there exists an element $e \in X$ which could have
been the part of last partition. For that, following conditions should be fulfilled:
- Let $g(X - \{e\})$ be $r$. Then $r \neq -1$.
- $r + e \le s$

If no such $e$ exists, then $g(X) = -1$, otherwise $g(X) = (r + e) \bmod s$

> Note that if $g(X) \neq -1$, then $g(X)$ is fixed irrespective of how the elements
> are partitioned because it is simply $(\sum\limits_{e \in X}{e}) \bmod s$.

Finally, if the complete set is denoted by $A$, a solution exists if $g(A) = 0$.

***