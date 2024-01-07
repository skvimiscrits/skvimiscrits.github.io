---
layout: page
title: Fundamentals - 1
parent: Algorithms
math: true
nav_order: 6
---

## Prefix sums

Given array $a$ of $n$ integers, we define the
prefix sum array $p$ of $a$ as:

$$
p_i = \sum\limits_{1 \le j \le i}{a_j}
$$

A prefix sum array can be computed in $O(n)$ using this
simple definition:

$$
p_i = \begin{cases}
a_1 & \text{if } i = 1 \\
p_{i-1} + a_i & \text{otherwise}
\end{cases}
$$

### Illustration 1

You are given an integer array $a$ of length $n$ and
$q$ queries of form $l, r$ in which you have to find
subarray sum from index $l$ to $r$. The number of
queries may be large.

#### Solution

We will pre-compute prefix sum array in $O(n)$.
Let $p_0 = 0$ for convenience.

We can then answer in each query in $O(1)$.
Sum $a_l + \ldots + a_r$ is given by $p_r - p_{l-1}$.

### Illustration 2

Given an integer array $a$ of length $n$,
find number of subarrays having sum $0$

#### Solution

If we have prefix sum array $p$, then we simply need to
find number of pairs $i, j$ ($i \lt j$) such that $p_i = p_j$.

This can be done using a hash-table (to store frequencies of elements in $p$)

### Illustration 2.1

You are given a string consisting of characters $\text{a}$ and $\text{b}$
Find the number of substrings

#### Solution

Map $\text{a}$ to $1$ and $\text{b}$ to $-1$.
We have reduced this problem to subarrays with sum $0$.

### Illustration 2.2

You are given a string consisting of characters from set $\{\text{a}, \text{b}, \text{c}, \text{d}\}$.
Find number of non-empty substrings containing:
- Equal count of $\text{a}$ and $\text{b}$.
- Equal count of $\text{c}$ and $\text{d}$.

#### Solution

Here we treat $\text{a}, \text{b}$ and $\text{c}, \text{d}$ independently.
Store prefix sum in form $(u, v)$ where $u$ is sum of $\text{a}, \text{b}$ (mapped to $1, -1$)
and $v$ similarly defined for $\text{c}, \text{d}$.

We will be interested in number of same pairs $(u, v)$.

### Illustration 3

Given integer array $a$ of length $n$, find number of subarrays
with sum $x$.

#### Solution

In this case we will look for pairs $i, j$ ($i \lt j$) such that
$p_j - p_i = x$ (it denotes that range $[i+1, j]$ has sum $x$).

We can use a hash table to implement this.
For convenience we can use $p_0 = 0$. We can then iterate
from $0$ to $n$, adding frequency of $p_i - x$ to the answer before
adding $1$ to frequency of $p_i$ in the hash table.

### Illustration 4

You are given an integer array $a$ of length $n$. Find a rotation
of this array such that all prefix sums are positive or report if none exists.

#### Solution

Let $p$ denote the prefix sum array of $a$.
If $p_n \lt 0$ then no solution exists, this is because
$p_n < 0$ for any rotation.

We will show that if $p_n \ge 0$ then a solution always exists.

Let $k$ be largest index such that $p_k = \min{(p_i)}$.
If $p_k \ge 0$ then the condition is already satisfied.
Otherwise $p_k \lt 0$ and thus $k \lt n$. We will analyse this case.

Let us consider last $n-k$ elements, i.e., range $[n-k+1, n]$
- There will not exist any negative prefix sum of this range exclusively.
  If there is, it contradicts the definition of $k$ and $p_k$ above.
- Overall sum $s$ of this range is $p_n - p_k$ which is positive
  because $p_n \ge 0$ and $p_k \lt 0$.

We can take first $k$ elements and place them at the end (left rotate $k$ times). Then:
- First $n-k$ elements correspond to those discussed above.
  These will not have any negative prefix sum. Overall sum will be $p_n - p_k$.
- Next $k$ elements have least prefix sum  $p_k \lt 0$ (considering only those $k$ elements).
  Since we added $n-k$ elements before these, we will have least prefix sum $(p_n - p_k) + p_k = p_n$
  which is non-negative.

### Illustration 5

Given integer array $a$ of size $n$, and an integer $k$,
find maximum subarray sum not exceeding $k$.

Constraints:
- $1 \le n \le 10^5$

#### Solution

Let $p$ be the prefix sum array corresponding to $a$.

For such subarray ending at $i$, we will need to find
$p_j$ ($j \lt i$) such that
- $p_i - p_j \le k \Rightarrow p_j \ge p_i - k$
- $p_i - p_j$ is largest possible, i.e., $p_j$ is smallest possible.

These constraints hint at a binary-searchable solution.
We can put all $p_j$ such that $j \lt i$ in some ordered set
and perform a binary search.

## Subarray with maximum sum

Given array $a$ of $n$ integers,
find the subarray with maximum sum.

### Divide and conquer algorithm

Let $f(l, r)$ denote max subarray sum in range $l, r$.

If $l \lt r$, then let $m = \lfloor\frac{l+r}{2}\rfloor$ and:

$$
f(l, r) = \max[f(l, m), f(m+1, r), g(l, m, r)]
$$

Here, $g(l, m, r)$ denotes max subarray sum crossing
both ranges $(l, m)$ and $(m+1, r)$. This can be
computed in $O(r - l + 1)$.

Time complexity is given by:

$$
T(n) = 2T(\frac{n}{2}) + O(n)
$$

which by Master's theorem results to $T(n) = O(n\lg{n})$

### Linear time algorithm

Let $f_k$ denote maximum subarray sum ending at index $k$.

The idea is to remove a prefix in range $[1, k]$.

Suppose the smallest (in terms of length) negative sum prefix is $[1, i]$.
There won't be any non-negative sum *suffix* in $[1, i]$ (otherwise $i$ will not be the smallest),
thus it will never be included in the maximum sum subarray ending at $k$.
We can discard this range.

We repeat this until there is no negative sum prefix left.
If there is no such prefix left, then remaining range contains
the maximum sum.

Here is sample code for computing $f$:

```py
f = [0] * n
s = 0
for i in range(n):
  s += a[i]
  if s < 0: # discarding the negative sum prefix
    s = 0
  f[i] = s 
return max(f)
```


## Number of inversions

We are given an array $A$ of size $N$ consisting of integers.
We need to find the number of pairs $(i, j)$ such that $A_i \gt A_j$ and $i \lt j$.

**Approach 1**

Use merge sort.

**Approach 2**

Use simple sum segment tree. Process elements in order of index.
- If $a_i = x$ is processed, add to inversion count the number of elements greater than $x$ in segment
  tree at this point.
- Then add $x$ to the segment tree before proceeding to $a_{i+1}$.

Note: This approach will work for values of $a$ in a small range.
For larger values, use sparse segment tree.

### Illustration

You are given an array $A$ of $N$ integers.
Find the number of its non empty subarrays having a non-negative sum.

Constraints:
- $1 \le N \le 10^5$
- $-10^9 \le A_i \le 10^9$

**Solution**

Let $B$ be the *prefix sum array* of $A$. That is:

$B_i = A_1 + \ldots + A_i$ $\forall$ $i, 1 \le i \le N$

Let $B_0 = 0$ for convenience.

First let's find the number of negative sum subarrays.

It holds for all $i, j$ such that $1 \le i \le j \le N$:

$$
(A_i + \ldots + A_j < 0) \Leftrightarrow (B_{i-1} > B_j)
$$

Thus, finding the number of inversions in $B_0, \ldots, B_N$ will
give us the number of negative sum subarrays. Let their
count be $K$.

The number of non-negative subarrays is then given by
$\binom{N}{2} - K$

***