---
layout: page
title: Searching - 1
parent: Algorithms
math: true
nav_order: 3
---

## Introduction

Searching generally refers to finding a particular entity within a given space.
A simple example is: given a list of $n$ integers, check if there exists $x$ in this list.
Here the *search space* is the list of $n$ integers and *target* is $x$.
For this task, we have to iterate over the whole list taking $O(n)$ time in the worst case.
What if this list is sorted?

Search spaces may not be explicit. For example: given a list of $n$ integers where
$i$-th integer is given as $2i + 3$, check whether $x$ is present in this
list. $n$ may be as large as $10^9$. Here, the search space is well defined
but not explicitly provided to us. We can make use of simple math to check
whether $x$ will be present resulting in an $O(1)$ search.
Hence, patterns in input spaces can lead to efficient algorithms.

## Binary search

Binary search is applicable on monotonic functions (i.e., non-increasing / non-decreasing).
The idea of binary search is to divide the search space into two halves and computing
the value of the *middle* element to determine which half contains the answer.

To find a value $x$ in a sorted list, we can use binary search:
- Find the middle element $m$ in the range.
- If $m \lt x$, recursively search the second half.
- Otherwise, recursively search the first half.

> Note: For the above algorithm to work, the list must be random access.
> We interchangably use terms lists and arrays but list can also refer
> to a linked list, though rarely.

In general, the following problem can be solved using binary search: given a boolean valued function $f(x)$ which
is always false below a certain value and true otherwise, and having domain $[A, B]$,
we are asked to find the smallest $k \in [A, B]$ for which $f(k)$ is true
or report whether there doesn't exist such $k$.

Binary search algorithm as C++ code:
```cpp
int search () {
  // assume boolean valued function f with domain [A, B]
  int l = A, r = B + 1;
  while (l < r) {
    int m = (l + r) / 2;
    bool res = f(m);
    if (res) r = m;
    else l = m + 1;
  }
  return (l == B + 1 ? -1 : l);
}
```

If $f$ takes $O(n)$ to compute and domain size is $m = B - A + 1$, then
binary search algorithm works in $O(n\lg{m})$.

### Implementation details

Note that $m = \lfloor \frac{l + r}{2} \rfloor$.
The maximum value of $m$ throughout the execution
can be $B$. This can only take place if the `else`
block is executed in each iteration.

We can also look at the near-terminal ranges of size $2$ and $3$:
- For range size $2$, $m = l$ and thus we need to be careful
  about not assigning $m = l$ in the `else` condition.
  In both cases ($f(m)$ being true or false), range size
  is reduced to one.
- For range size $3$, $m = l + 1$. Then, it either
  results into a range of size $2$ or $1$.

After `while` loops ends, there can be two cases:
- $l = B + 1$
  - In this case last value of $m$ was $B$ and $f(B)$ turned
    out to be false resulting in $l = B + 1$
  - This means that $f(x)$ is false for all $x \in [A, B]$.
- $l \le B$
  - $l$ is the smallest value such that $f(l)$ is true.

### Alternate implementation

An alternate way of implementing binary search is by working on *bits* of the answer. We start off from highest bits down
to $2^0$.

Sample implementation:
```cpp
// assume boolean valued function f with domain [0, n]
// n can be represented by M bits
// find smallest k such that f(k) is true
int ans = 0;
const int M = 30;
for (int i = M; i >= 0; i--) {
  if (ans + (1 << i) >= n) continue;
  if (!f(ans + (1 << i)))
    ans += (1 << i);
}
return ans;
```

### Reducing problems to binary search

For the simple problem of finding an integer $x$ in a sorted list $a$ of $n$ integers,
we have:
- $f(i) = a_i \ge x$ (we are finding smallest index $k$ such that $a_k \ge x$)
- The search either returns $-1$ or a valid index $k$ in $[1, n]$. If $x$ exists,
  it must hold that $a_k = a_x$.
- Since $f$ computes in $O(1)$ and domain size is $n$, time complexity is $O(\lg{n})$.

#### Identifying patterns in problems

Problems may not appear binary-searchable at first glance.
Consider the following problem:

Given a list of $n$ integers $a_1,\ldots,a_n$ and an integer $k$,
partition the list into $k$ contiguous parts to minimize
the maximum sum among all parts.

Constraints:
- $1 \le k \le n \le 10^5$
- $1 \le a_i \le 10^9$

Consider the boolean function definition: $f(x)$ denotes
whether it is possible to partition the list into $k$ parts
with each part having sum not greater than $x$.

Before using binary search, we need to verify that $f(x)$
is monotonic. Suppose for some $x_1$, $f(x_1)$ is true
then for any $x \gt x_1$, $f(x)$ will be true because
we can simply use the partition created using $x_1$.

Note that in this case we need to find the *largest*
$x$ for which $f(x)$ is true.

Computing $f(x)$ is straightforward: greedily choose elements
until sum does not exceed $x$. If an element can't be added
without exceeding the sum, start a new partition.
Count the partitions used at the end.

> Note that empty partitions
> can always be filled by using elements from other partitions because
> $a_i$ is positive.

A rough upper bound on maximum sum with given
constraints is $m \cdot n$ where $m = \max{a_i}$.
Thus, domain size for $f$ is $n \cdot m$ and computing $f$ takes $O(n)$.

The overall time complexity comes out to be $O(n \lg {nm})$.

*Assuming* that this term also gives us the number of operations (roughly),
we can plug in the maximum values: $10^5 \lg {10^{14}} \approx 3.45 \cdot 10^6$

***