---
layout: page
title: Problem set - 1
parent: Algorithms
math: true
nav_order: 11
---

## Tuples with fixed sum

You are given integer $x$ and a list of $n$ integers,
$a_1, \ldots, a_n$ (all distinct).
Find all pairs $(i, j)$ such that $a_i + a_j = x$

#### Solution

We can keep a mapping of value to index and
for some $u$, we can find the index of $x - u$ (if exists).
Time complexity is $O(n)$.

#### Variation: list $a$ is sorted.

In this case we can use a two-pointers approach.
The first pointer goes forward while the second one goes backward
since the list is sorted and both should add up to a constant value $x$.

This can be extended to find triples and quadruples having sum equal $x$
in $O(n^2)$ and $O(n^3)$ repectively.
- For the case of triples, we fix the first element at index $i$
  and find a pair $(j, k)$ in $[i+1, n]$ such that $a_i + a_j + a_k = x$.
- For the case of quadruples, we fix first two elements at indices $i$ and $j$
  and find a pair $(k, l)$ in $[j+1, n]$ such that $a_i + a_j + a_k + a_l = x$.

#### Variation: $a$ may contain duplicates and distinct tuples (of values) required

In this variation we should return distinct tuples of form $(a_i, a_j)$.
Suppose $a_j = a_k$ then if we are including $(a_i, a_j)$, then $(a_i, a_k)$ should
not be included in the result.

One simple solution is to use a hash table equivalent to return distinct tuples.

If we want to avoid additional space usage, we should
consider only first element in a segment of duplicates.
Precisely, skip $a_{i+1}$ (first pointer) in case $a_i = a_{i+1}$.

## Maximizing minimum sum from two lists

Given $N$ and two lists $A$ and $B$ of length $N$ with $1 \le A_i, B_i \le M$
- For each index, choose one element either from $A$ or from $B$.

Find max of $\min(S_A, S_B)$ where $S_A$ is the sum of elements
chosen from $A$ and $S_B$ is similarly defined.

**Solution**

Let sum of all $B$ is $M_B$

Suppose we want to check whether we can make a an answer $K$
then we need to choose elements from $A$ such that their sum is not less than $K$
and at the same time their counterpart sum should not exceed $M_B - K$.

This converts the problem to the classic **0-1 Knapsack problem**.
Here the values are $A_i$ and weights are $B_i$ and we have to
maximize the value with weight limit $M_B - K$.

> Recall that knapsack problem has a simple definition $f_{i, w}$ as
> max possible value from elements $1,\ldots,i$ within weight $w$
> and solvable in $O(N \cdot W)$ where $W$ is max weight.

## Container with most water

Given $n$ vertical bars of height $h_1, \ldots, h_n$ (all positive) and width one,
find the *container* with most water if we pour water from above.

Precisely, let $f(i, j) = \min(h_i,h_j) \cdot \vert j - i \vert$.
Find the maximum value of $f(i, j)$ over all pairs $i,j$.

#### Solution 1

We can process elements in non-decreasing order of heights.
- Consider the minimum $h_i$ among all present
- Then we need to find $j$ such that $h_i \cdot \vert j - i \vert$ is maximized.
- We need the element at the farthest index, which will be one of
  minimum and maximum indices among all indices present.
- Remove $h_i$ after computing result and repeat for remaining.

We would need a ordered set for finding minimum and maximum elements
fast. For example: C++ `set<int>`.

#### Solution 2

Two pointers or window approach:

1. Let $i \leftarrow 1$ and $j \leftarrow n$ and $x \leftarrow 0$ initially
1. Let $x \leftarrow \max(x, f(i, j))$
1. if $h_i < h_j$ then  $i \leftarrow i + 1$ otherwise $j \leftarrow j - 1$
1. if $i > j$ return $x$
1. go to step 2.

To prove correctness we need to prove the following:
Just before setting $i \leftarrow i + 1$,
we can not get a solution with left end as $i$ which is better
than what's already computed.

Let $j$ be the right end at that point. We will prove this in two parts:
1. Right end in $[j+1, n]$ doesn't give a better solution (than already computed)
2. Right end in $[i+1, j-1]$ doesn't give a better solution than that for right end as $j$

Part 1:
Suppose some $r$ in $[j+1, n]$ as right end gave a better solution.
Let $l$ be the left end just before moving to $r - 1$ (step 3) for that right end.
We will perform $x \leftarrow \max(x, f(l, r))$.
Note that there will be such a move because $j \lt r$ and it will hold that $l \le i$ as we only go forward from left end.
We have $h_l \ge h_r$ (resulting in *otherwise* part of step 3).

We need to show that $f(l, r) \ge f(i, r)$. We have:
- $f(l, r) = h_r \cdot (r - l)$
- $f(i, r) = \min(h_i, h_r) \cdot (r - i)$

Since $\min(h_i, h_r) \le h_r$ (by simple logic) and $(r - i) \le (r - l)$, we have
$f(i, r) \le f(l, r)$.

Part 2:
Suppose some $r$ in $[i+1,j-1]$ as right end gave a better solution.
We need to show that $f(i, j) \ge f(i, r)$.
Just before setting $i \leftarrow i + 1$, we would have $h_i \lt h_j$.
We have:
- $f(i, j) = h_i \cdot (j - i)$
- $f(i, r) = \min(h_i, h_r) \cdot (r - i)$

Since $\min(h_i, h_r) \le h_i$ and $(r - i) \le (j - i)$, we have
$f(i, r) \le f(i, j)$.

## Distinct elements by adding to subsequences

We are given a list of $n$ elements that are $0$ initially.

In one operation we can add any integer $x$ to any subsequence.

What is the minimum number of operations to make all elements distinct?

#### Solution

Suppose we do $k$ operations in which we add $x_1, \ldots, x_k$

Max number of unique numbers we can create are $2^k$ (for example: taking $x_i$ as $2^i$)

Answer will be minimum $k$ such that $2^k \ge n$.

## Combine to maximize

*(Source: AtCoder 138C - Alchemist)*

You initially have a list of $N$ integers $A_1,\ldots,A_N$.

In one operation, you can remove two elements from the list
(if possible), let their values be $u, v$ and add element
$\frac{u + v}{2}$ to the list.

This is repeated until the list has only one value remaining.

Find maximum possible last value.

Constraints:
- $1 \le N \le 10^5$
- $1 \le A_i \le 10^9$

#### Solution

Intuitively, we combine the smaller elements first since
any element $x$ being part of $k$ operations (may not be individual)
retains only $\frac{x}{2^k}$.

If we assume sorted order, i.e., $A_1 \le \ldots \le A_N$,
then always combining two smallest will be the optimal solution.

But to prove this, we watch sum $S(A)$ of the (multi)set of elements $A$.

Initially, $S(A) = A_1 + \ldots + A_N$.

In each operation, we take two elements $u, v$ from $A$ and 
insert $\frac{u + v}{2}$ into $A$.

$S(A') = S(A) - \frac{u + v}{2}$

If we greedily take out only smallest elements, the
reduction will be minimum (**needs proof !!??**)

## Maximum in fixed length sliding window

Given a list of $n$ integers, find maximum in each window of size $k$ (i.e., $n - k + 1$ values)

#### Approach 1

Using a structure which allows fast insertions, deletions and finding maximum.
- Can use C++ `set<pair<int, int>>` or `multiset<int>` or `map<int, int>` or equivalent.

This will result in $O(n \lg{n})$ time.

#### Approach 2

Use a double-ended queue to maintain sorted list of values.
Iterate through elements and:
- To insert an element, remove all lesser elements to maintain sorted order.
- Remove maximum element if it has fallen out of window. 

```
# Input: A[1...N]

D = deque()
res = []
for i in 1...N:
  while D.size > 0 and A[D.front] < A[i]:
    D.remove_front()
  D.insert_front(i)

  if D.back == i - k:
    D.remove_back()
  
  if i >= k:
    # Insert window max
    res.add(A[D.back])

return res
```

This algorithm satisfies the following invariants after each iteration:
- Elements in `D` are in decreasing order from front to back because `i`
  is inserted in iteration `i`.
- If `D = [i1, i2, ...]` from front to back, then it holds that `A[i1] <= A[i2] <= ...`
  because we remove all smaller elements before insertion.
- Before inserting window max:
  - `D` contains at least one element (`i` will always be present)
  - `D` never contains any element outside present window `i-k+1` to `i`.
    Suppose it does contain such element. Then back element will also be such
    an element because of decreasing order. Let it be `x` (`x < i-k+1`).
    But it should have been removed at iteration `x+k <= i`.

Time complexity is $O(n)$ since we never insert an element twice.

***