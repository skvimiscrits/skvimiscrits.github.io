---
layout: page
title: Recursion
parent: Algorithms
math: true
nav_order: 10
---

### Generating all permutations:

```
vector<int> a; // let size be n

void f (int i) {
  if (i == n - 1) { output(a); return; }
  solve(i + 1);
  for (int j = i + 1; j < n; j++) {
    swap(a[i], a[j]); /* change state */
    solve(i + 1);
    swap(a[i], a[j]); /* restore state */
  }
}
```

What if there are duplicates and we only want
unique permutations?

To make `f` defined above generate only unique
permutations, we must **avoid duplicate-swap**
as in `f(i)` we must have different elements
for `a[i]` before generating all possible
permutations for `a[i+1...n]`.
Two cases of duplicate swap:
- Swapping `a[i], a[j]` where `a[i] = a[j]`
- Swapping `a[i], a[j]` but a swap `a[i],a[k]`
  was done earlier with `a[j] = a[k]`.

### Generating all combinations of size $k$

From given array $A$ of length $N$, generate
all combinations of $k$ elements.

We can do recursive backtracking - taking an
element $a_i$ and then take rest of the elements
from $a_{i+1},\ldots,a_n$ if possible.

### All possible subsets

Recursive backtracking

To generate all subsets of $a_i,\ldots,a_n$
- take element $a_i$
  generate subsets from $a_{i+1},\ldots,a_n$
- repeat without taking $a_i$

### All posssible subsets without duplicates

- Make list of unique elements and frequencies
- Apply same concept as above with a differnce that
  we take 0, 1, ... frequency(x) occurrences of element x

### Search for a sequence in a grid

Given $N \times M$ grid $G$ of elements,
find if there exists a sequence of adjacent cells
(without using same cell twice) $A_1,\ldots,A_K$ (two cells are adjacent if they share a side)

**Ideas**

Recursive backtracking starting from some cell $(i, j)$
such that $G_{i,j} = A_1$.

But how do we measure the time complexity?

- At each cell we have atmost 3 choices (except for
  the initial cell)
  - This is a loose upper bound as in many cases we
    will have 2, 1 or even no choices.
- We can visit each cell atmost once

Let $f(x, y)$ be the number of operations reqd. for
$x$ cells and search sequence of length $y$ then:

$f(x, y) = 3f(x-1, y-1)$

and $f(x, 0) = 1$ because we have covered all elements of search seq.

Thus $f(x, y) = 3^y$ as a rough upper bound

We get a loose upper bound of time complexity $O(N \cdot M \cdot 3^K)$

*Pruning* 

- Checking whether the present bounded area
  (bounded by already selected cells) has all elements 
  required for the remaining search sequence


***