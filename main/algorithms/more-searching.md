---
layout: page
title: Searching - 2
parent: Algorithms
math: true
nav_order: 9
---

## Rotated sorted array

A sorted integer array $a$ having *distinct values* is rotated right $k$ times ($0 \le k \lt n$).
Find index of value $x$ in this array or report if not present.

#### Approach 1

We first find $k$ and then use it as offset, i.e.,
let $a'$ be the original array, then $a'_i = a_{(i+k) \bmod n}$.

We perform binary search twice in this approach.

#### Approach 2

For range $l, r$ and mid index $m$, there are two cases:
- $a_l < a_r$ which means $[l,r]$ is sorted and search space can be reduced like a normal binary search.
- $a_l > a_r$ which means there must be some point $k$ such that
  - range $[l, k]$ is sorted
  - range $[k+1, r]$ is sorted

  Here range $[l, m]$ is sorted if $m \le k$
  otherwise $[m, r]$ is sorted.
  Now, where do we expect our target to be?
   - We first check the sorted range: if target may lie within it, we
     explore that range. Otherwise we explore the unsorted range.
     This is because if the sorted range has minimum and maximum as $u$ and $v$,
     then the unsorted range can not contain any number in $[u, v]$.
     This observation arises from rotation & distinct sorted elements.

#### Variation: non-distinct elements

With non-distinct elements, the worst case running time is $O(n)$.
Though a simple linear search will also be $O(n)$, we provide an
algorithm which works *generally* good.

We state the observation used before:
If two sub-arrays have elements from *disjoint* ranges and one of them is sorted,
we can determine where value $x$ may lie (by first checking the sorted one).

Extending approach 2 above,
for range $l, r$ and mid index $m$ and target $t$, there are cases:

- Eliminate edge cases by checking for $a_l$ or $a_r$ or $a_m$ equal $t$.
  This is because we may have duplicates. It might be possible that $a_m = a_{m+1}$ or $a_l = a_r$
  which means that the ranges are not really disjoint. After checking
  these cases, we consider the ranges effectively disjoint.

- $a_l \lt a_r$

  Here we can be sure that range $[l, r]$ is sorted (even if elements are non-distinct)
  and we can use normal binary search.

- $a_l = a_r$

  Range $[l, r]$ may not be sorted. A simple example is $[l, r-1]$ being increasing and $a_l = a_r$
  (in that case all elements in ranges $[1, l]$ and $[r, n]$ will have same value).
  We will check if we can find a sorted range on splitting into two, there can be cases:
  - $a_l = a_m$

    We can not determine where hinge point is and *need to search both halves*.
    Hinge point is the index $k$ such that $a_k \gt a_{k+1}$ (which is the last element in original array).

  - $a_l \lt a_m$
  
    Hinge point $k$ present in range $[m, r]$ and range $[l, m]$ is sorted.
    If target $t$ may be present in sorted range (by checking minimum and maximum),
    search there. Otherwise search the other range.

  - $a_l \gt a_m$
  
    Hinge point $k$ present in range $[l,m)$ and range $[m+1,r]$ is sorted.
    Again, if target $t$ may be present in sorted range, search it there.
    Otherwise search in the other range.

- $a_l \gt a_r$

  Hinge point lies in $[l, r)$.
  For $a_m$, we have:
  - $a_l < a_m$ then range $[l, m]$ is sorted.
  - $a_l = a_m$ then all elements range $[l, m]$ have same value (which is sorted).
  - $a_l > a_m$ then hinge point lies in $[l, m]$ and thus $[m+1, r]$ will be
    sorted.

We can summarize the solution as follows:
- Check whether any of $a_l, a_r, a_m$ equal $t$ and return if yes.
- If $a_l \lt a_r$ then perform simple binary search
- Otherwise,
  - If $a_l = a_m = a_r$ then perform search on both halves.
  - If $a_l \lt a_m \land t \in [a_l, a_m]$ or $a_l \gt a_m \land t \notin [a_{m+1}, a_r]$
    then search the first half.
  - Otherwise search the second half.

## Ternary search

Search for a value $x$ in an integer array $a$
of length $n$ satisfying (for some $1 \le k \le n$):
- $a_1 \lt \ldots \lt a_k$
- $a_k \gt \ldots \gt a_n$

#### Solution - two phases

We can first find $k$ and then perform simple binary search.

To find $k$, we first divide the ranges using middle element $m$
and based on comparison $a_m \lt a_{m+1}$ we can determine whether
$k$ lies in first half or the second.

## Illustrations

### Illustration 1: Searching in sorted 2D matrix

Find position of $x$ in a $n \times n$ matrix $a$ whose rows
and columns contain values in sorted order (non-descending)
or report if it doesn't exist.

#### Solution

We can perform binary search on individual rows
to solve it in $O(n\lg{n})$.
But we are not taking advantage of columns also being sorted.

Assume that we are searching for $x$ in some column $c$
and all columns to right of $c$ have been explored.
We can try to find $x$ by going *down*. These cases can occur:
- $x$ is found
- Some $y \gt x$ occurs (at some row $r$)
- We reach end of column: in this case $x$ is not present in the matrix.

In the second case, we will start looking for $x$ in column
$c - 1$. But do we start from top? Given the sorted nature,
we can start from row $r$ itself.

To summarize:
- Start from top left cell $(r, c) = (1, n)$
- Move down the column until one of the following occurs:
  - $a_{r, c} = x$ then return $(r, c)$.
  - $a_{r, c} \gt x$, then set $c \leftarrow c - 1$.
    - If $c \lt 1$ then return not found else repeat.
  - $r \gt n$ then return not found.

### Illustration 2: Find element with order in sorted 2D matrix

Find $k$-th smallest element in a $n \times m$ matrix whose
rows and columns contain values in sorted order (non-descending).

#### Solution

Define $f_x$ as number of elements not greater than $x$
in the matrix.
$f_x$ can be computed in $O(n + m)$ for some $x$ (using
the method described in the search part above).

Suppose the domain of values in the matrix is $[p, q]$,
then we can binary search on $f$. What definition should
be chosen out of the following:
- Find the smallest $x$ such that $f_x \ge k$
- Find the largest $x$ such that $f_x \le k$

The second one is not correct because there can be duplicate
values. Take the following matrix for example:

$$
\begin{bmatrix}
1 & 2 \\
2 & 3
\end{bmatrix}
$$

For $k = 2$, what is the largest $x$ such that $f_x \le 2$?
The answer comes out to be $1$ which is incorrect. This
is because there are $3$ elements ($1, 2, 2$) not
greater than $2$ in the matrix.

***