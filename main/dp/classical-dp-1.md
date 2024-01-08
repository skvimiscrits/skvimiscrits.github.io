---
layout: page
title: Classical problems - 1
parent: DP
math: true
nav_order: 2
---

## 0-1 Knapsack

You are given $N$ items numbered $1$ to $N$ having
with $i$-th item having value $v_i$ and weight $w_i$.

You have a bag that can hold maximum weight $W$.
Find maximum total **value** of items that this bag can hold.

Note: $W, w_i \in \mathbf{N}$

**Solution**

Let $F_{i, x}$ be optimal value when weight limit
is $x$ and items from $1$ to $i$ are chosen.

We can define $F$ recursively:

$$
F_{i, x} = \begin{cases}
v_i & \text{if } i = 1, w_i \le x \\
0   & \text{if } i = 1, w_i \gt x \\
\max(v_i + F_{i-1,x-w_i}, F_{i-1,w}) & \text{if } i > 1, w_i \le x \\
F_{i-1,w} & \text{if } i > 1, w_i \gt x
\end{cases}
$$

The third case corresponds to the choice of including $i$
in current selection. If included, value $v_i$ is added
but weight limit becomes $x - w_i$.

We can implement the solution in $O(N \cdot W)$.
Since total number of states is $N \cdot W$,
we can compute each state in $O(1)$ **reusing**
the already computed values (for smaller $i$).

> Note: If we wish to produce such a selection
> as output, then one way is to store the choice at
> each decision point and then backtrack from final state.

## Longest common subsequence

Given two integer sequences $A$ and $B$ of length $N$ and $M$,
find the longest common subsequence.

**Solution**

We will show how this can be broken into independent subproblems.

Let $F_{i, j}$ denote the length of longest common susequence (LCS)
of sequences $[A_1, \ldots, A_i]$ and $[B_1, \ldots, B_j]$.

We can recursively define $F$:

$$
F_{i, j} = \begin{cases}
0 & \text{if } i = 0 \text{ or } j = 0 \\
\max(F_{i-1, j}, F_{i, j-1}, 1 + F_{i-1,j-1}) & \text{if } A_i = B_j \\
\max(F_{i-1, j}, F_{i, j-1}) & \text{otherwise}
\end{cases}
$$

The second case covers the choice of selecting $A_i = B_j$ as a common element.

Both second and third case also capture the possibility of either $A_i$ not used or $B_j$ not used
(because if both were used, $A_i = B_j$ would hold and we have covered that possibility)

> $F_{N, M}$ will give the length of LCS. To recover the actual LCS,
> one way is to store the choices taken (i.e, which maximum element was chosen) at each state computation
> and backtracking from final state.

There are $N \cdot M$ states and each state can be computed in $O(1)$
by reusing already computed states. We can implement this in $O(N \cdot M)$.

## Matrix chain multiplication

> *Background*
> 
> If you have matrices $A_{N \times M}$ and $B_{M \times K}$,
> then computation of $A \cdot B = C_{N \times K}$ requires $N \cdot M \cdot K$ operations
> to compute assuming a trivial algorithm is used.

Given dimensions of ordered sequence of $N$ matrices,
find the order of multiplication (or parenthesisation) such that number of operations required for their multiplication is minimized
(assuming trivial algorithm being used)

Dimensions of $i$-th matrix are $a_i \times b_i$
and it holds that $a_{i+1} = b_i$ for all $1 \le i \lt N$.

**Solution**

Let's look at the last multiplication operation: it will
be among product of matrices in $[1, \ldots, i]$ and $[i+1, \ldots, N]$ for some $i$ ($1 \le i \lt n$).

Since we do not know $i$ beforehand, we will enumerate solutions for all possible $i$.

Let $F(l, r)$ denote minimum number of operations required for multiplication
of matrices $[l, \ldots, r]$.

We can define $F$ recursively:

$$
F(l, r) = \begin{cases}
0 & \text{if } l = r \\
\min\limits_{l \le i \lt r}\left(F(l, i) + F(i+1, r) + a_l \cdot b_i \cdot b_r \right) & \text{otherwise}
\end{cases}
$$

There are $N^2$ possible states and each state requires $O(N)$ computation by **reusing** already computed values (assuming addition/multiplication as constant time operation).
We can implement the solution in $O(N^3)$.

## Longest increasing subsequence

Given an integer array $a$ of length $n$, find the longest
increasing subsequence.

### Solution 1

We can define $f_i$ to be the length of longest subsequence
ending at $i$.

$$
f_i = \begin{cases}
1 & \text{if } i = 1 \\
1 + \max{\{f_j \vert (1 \le j \lt i) \wedge (a_j \lt a_i) \}} & \text{otherwise}
\end{cases}
$$

We can also define $g_i$ as the index previous element in the sequence
to recover the sequence.

The last index of the LIS is $\argmax\limits_{1 \le i \le n}{f_i}$

This computation takes $O(n^2)$ time.

### Solution 2 - Faster

We define $f_i$ differently this time:
*minimum element at which LIS of length $i$ ends*

We will compute $f$ using the following algorithm:

- Let $f_i = \infty$ for $1 \le i \le n$
- For each $i$ from $1$ to $n$ do:
  - Let $j$ be the smallest index such that $f_j \ge a_i$
  - Do $f_j \leftarrow a_i$

We compute $f$ for $1 \le i \le n$ because length of LIS
can be $n$ at most.
- $f_i = \infty$ denotes that no increasing subsequence of $i$
  can exist.
- If $f_i = \infty$ for some $i$, then $f_j = \infty$ for all $j$ such that $i \lt j \le n$

The algorithm can be explained as:
- If current element is $a_i$,
  then what is the LIS (considering only elements till $i-1$),
  to which we can append $a_i$?
- We would need to find minimum $j$ such that $f_j \ge a_i$.
  If $j = 1$ then $a_i$ can only be the single element in the
  sequence. Otherwise, $a_i$ can be appended to the sequence ending at 
  $f_{j-1}$ because $f_{j-1} \lt a_i$
- LIS can be recovered by storing indices along with the values
  in $f$ and storing the index of previous element in the sequence.

Time complexity of this approach is $O(n \lg{n})$.

### Illustration 1

You are given a list $p$ of $n$ integer pairs such that $p_i = (a_i, b_i)$.
You can re-order this list in any way. Find the longest sequence
of pairs $q$ such that $a_i \lt a_j$ and $b_i \lt b_j$
for all $i, j$ where $q_i = (a_i, b_i)$ and $q_j = (a_j, b_j)$ and $i \lt j$.

#### Solution

Assume that $p$ is sorted by the first element $a_i$ of each pair.
Then for any subsequence of $p$, it will hold that $a_i \le a_j$
where $i \lt j$. It can be proved that the solution will be
subsequence of $p$.

We can now find the LIS of $p$, looking only at $b_i$ of each pair.

But it is possible that such subsequence may have $a_i = a_{i+1}$.
We have to change the sorting order in order to prevent cases like $b_i \lt b_{i+1}$
and $a_i = a_{i+1}$. This can be done by sorting on $b_i$ in descending
order where $a_i = a_{i+1}$. This will ensure that only a single element
is chosen to be part of LIS from a contiguous block of $a_i = a_{i+1} = \ldots = a_j$.

***