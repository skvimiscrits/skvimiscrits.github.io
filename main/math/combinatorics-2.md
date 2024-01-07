---
layout: page
title: Combinatorics - 1
parent: Math
math: true
nav_order: 8
---

## Finding next permutation in lexicographical ordering

You are given $A$ ($A_1 \ldots A_N$) which is a permutation of $1 \ldots N$ ($N \ge 2$).
Find the lexicographically smallest permutation greater than
$A$. Report if it doesn't exist.

**Solution**

We first define lexicographical order in permutations.

For permutations $A$ and $B$ of $1,\ldots,N$ we say 
$A \lt B$ if there exists index $k$ such that 
- $A_i = B_i$ $\forall$ $1 \le i \lt k$
- $A_k \lt B_k$

For convenience in discussion, let's call $k$ defined above as the **deciding index** (in context of comparing two permutations).
There is no deciding index in case the compared permutations are equal.

In simple terms, we compare both permutations starting
from index 1 and we report lesser or greater if we find different
elements at some index. If we reach the end, the permutations are equal.

Now suppose we have permutations $A, B, C$ of $1,\ldots,N$.
Further suppose $A \lt C$ and $B \lt C$ and
let $p$ and $q$ be the deciding indices for comparison of $A,C$ and $B,C$ respectively.

If $p \lt q$  it holds that $B \lt C$ since $B_p = A_p$ but $A_p \lt C_p$
(note that $A_i = C_i$ $\forall$ $1 \le i \lt p$ 
and $A_i = B_i$ $\forall$ $1 \le i \lt q$)
This implies ordering $A \lt B \lt C$.

Thus we come to an important claim:

Claim 1: *Among all permutations greater than a permutation $A$, those with
greatest deciding index (w.r.t. comparison with $A$) are closest
to $A$ in lexicographical ordering.*

Now we come back to the given problem. For the given
permutation $A$, we need to find the next higher permutation
in the ordering. Let that permutation be $B$ (the solution).

Let $k$ be the deciding index w.r.t. comparison of $A$ and $B$
which means $A_i = B_i$ for $i = 1,\ldots,k-1$
and $A_k \lt B_k$.

Take, for example, $A = [3, 2, 5, 4, 1]$

We need to find the largest $k$ such that sub-permutation $A_k,\ldots,A_N$
is not the greatest among all permutations of its elements.
- We can start with $i = N$ (down to $2$) and stop at first occurence
of $A_{i-1} \lt A_i$ thus setting $k = i-1$ (proof is simple - for any index $j \gt k$ 
we have $A_j \gt \ldots \gt A_N$)
  - From the example, we will have $k = 2$ (as $2 \lt 5$ whereas $5 \gt 4$ and $4 \gt 1$)
- If no such $k$ exists, then $A_1 \gt \ldots \gt A_N$ meaning the
  permutation is the largest in lexicographical ordering.

Now, upon finding such $k$, we have:
- $A_k \lt A_{k+1}$
  - From the example, $2 \lt 5$
- $A_{k+1} \gt \ldots \gt A_N$
  - From the example, $5 \gt 4 \gt 1$

Till now we have $B_i = A_i$ $\forall$ $1 \le i \lt k$.
The remaining elements $B_k,\ldots,B_N$ have to be a permutation
of $A_k,\ldots,A_N$.
- Since it should be the case that $B_k \gt A_k$
  and $B$ being smallest such permutation, we need to have
  $B_k$ least possible, i.e. $B_k$ will be the
  the smallest element $A_t$ among $A_k,\ldots,A_N$ such that $A_t \gt A_k$.
  - From the example, $A_k = 2$ and $A_t = 4$
- From the remaining elements, we will form the smallest
  permutation (i.e. elements in sorted order) since we already have
  $B_k \gt A_k$ establishing $B \gt A$.
  - From the example, remaining elements in sorted order are $[1, 2, 5]$

Hence we obtain the solution $B$.

In the example, we get $[3, 4, 1, 2, 5]$ as next permutation of $[3, 2, 5, 4, 1]$.

**Obtaining next permutation in-place in O(n)**

If we want to *modify* $A$ to obtain the solution without
using any auxiliary space, we can do so by adding an observation to
the last part of the solution described above.

After finding the appropriate element $A_t$ for $B_k$
- Swap $A_k$ and $A_t$
- Reverse the part of sequence $A_{k+1},\ldots,A_N$.

After the swap, it still holds that $A_{k+1} \gt \ldots \gt A_N$ and the reverse
step makes it sorted.
Why?
- $A_t \gt A_k$ and it is the smallest such value.
- $A_{t-1} \gt A_k$ is obvious.
- $A_k \gt A_{t+1}$ because either $A_k \gt A_{t+1}$ or $A_k \lt A_{t+1}$ - the second case being impossible by the choice of $A_t$.

Sample implementation
```python
def reverse_inplace(a, s, e):
  for i in range((e - s) // 2 + 1):
    a[s + i], a[e - i] = a[e - i], a[s + i]

def next_perm(a):
  if len(a) < 2: return False
  k = len(a) - 2
  while k >= 0 and a[k] > a[k + 1]: k -= 1
  if k < 0: return False
  t = k + 1
  for i in range(k + 1, len(a)):
    if a[i] > a[k] and a[i] < a[t]: t = i
  a[k], a[t] = a[t], a[k]
  reverse_inplace(a, k + 1, len(a) - 1)
  return True

a = [3, 2, 5, 4, 1]
if next_perm(a): print(a)
# [3, 4, 1, 2, 5]
```

## Find permutatation at given index

Suppose you have all permutations of $1$ to $n$
arranged in lexicographical order (smallest to largest).


Find the permutation at index $k$
(assuming index of first permutation is $0$)

Constraints:
- $0 \le k \le (n!) - 1$

**Solution**

Let permutation at index $k$ be $p$.
Then $p$ will have exactly $k$ permutations
smaller than it.

> Note that here we label elements of a permutation
> as $1, 2, \ldots, n$ but these elements can be a
> subset of any ordered set of symbols. $1$ denotes
> the smallest element while $n$ denotes the largest
> element.

We know that all permutations starting with $1, \ldots, p_1 - 1$
will be smaller than $p$. There will be $(p_1 - 1) \cdot (n-1)!$
such permutations. We get the inequality:

$$
(p_1 - 1) \cdot (n - 1)! \le k
$$

$p_1$ will be the maximum possible integer satisfying this inequality
(we can show that any value lesser than maximum will not satisfy the conditions).

We get:

$$
p_1 \le \frac{k}{(n-1)!} + 1
$$

Since $p_1$ is maximum integer satisfying this:

$$
p_1 = \left\lfloor \frac{k}{(n-1)!} \right\rfloor + 1
$$

We can now reduce this problem to finding permutation
of length $n - 1$ at index $k - (p_1 - 1) \cdot (n - 1)!$

We will need to re-label the remaining elements from $1$ to $n-1$.

To implement this, we can have sorted list of symbols and remove
the obtained first element after each step in $O(n)$.
Hence our algorithm will run in $O(n^2)$.

```py
# assume fac(x) gives factorial of x in O(1)
def find_perm (n, k):
    p = [i + 1 for i in range(n)]
    r = []
    for i in range(n):
        x = k // fac(len(p) - 1)
        k -= fac(len(p) - 1) * x
        r.append(p[x])
        del p[x]
    return r
```

Here, `p` simply contains the initial ordering of some symbols.
Replacing the line `p = [i + 1 for i in range(n)]`
with `p = [chr(ord('a') + i) for i in range(n)]` will
use lowercase letters as symbols instead of $1, 2, \ldots, n$.

## Find index of permutatation

Suppose you have all permutations of $1$ to $n$
arranged in lexicographical order (smallest to largest).

Find the index of given permutation $p_1,\ldots,p_n$
assuming index of first permutation is $0$.

Constraints:
- $p_1,\ldots,p_n$ is a permutation of $1, \ldots, n$.

**Solution**

In other words, we need to find the number of permutations
smaller than the given permutation.

Since $p_1$ is the first element. All permutations starting
from $1, \ldots, p_1 - 1$ will be smaller than $p$.

Thus, at least $(p_1 - 1) \cdot (n - 1)!$ permutations are smaller.

From $p_2, \ldots, p_n$ we can re-label the elements according to their
relative order and we get a subproblem of size $n - 1$.

To implement this, we can have a list $[1, 2, \ldots, n]$ initially.
Upon processing $p_1$, we can remove $p_1$ from this list. Rest of the element
will have relative ordering. We can get $i$-th smallest element at index $i$.

```py
# assume fac(x) gives factorial of x in O(1)
def find_index (p, n):
    r = [i + 1 for i in range(n)]
    k = 0
    for i in range(n):
        x = r.index(p[i])
        k += x * fac(len(r) - 1)
        del r[x]
    return k
```

***