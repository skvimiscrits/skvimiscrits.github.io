---
layout: page
title: Fundamentals - 2
parent: Algorithms
math: true
nav_order: 7
---

## Minimum segment cover / Interval covering

Given $N$ segments $[L_i, R_i]$ ($1 \le L_i \le R_i \le M$),
find minimum number of segments that collectively cover $[1, M]$ or report
if this is impossible.

### Solution

Out of segments having same $L_i$, we can discard all but one
having maximum $R_i$. Let the segments be sorted by $L_i$.
and numbered $1,\ldots,N$.

**Greedy strategy**:

We start by selecting segment $1$ (it should hold that $L_1 = 1$)
and range $[1, R_1]$ is covered.

In general if current selection from segments $1,\ldots,i$
covers $[1, r]$, next chosen segment $j$ should have:
- $L_j \le r$
- $R_j$ is maximum possible

**Claim**:
Let the optimal solution be chosing $P$ segments $t_1,\ldots,t_P$
(with $t_i \le t_{i+1}$). By the greedy strategy defined above, if
chosen segments $x_1,\ldots,x_i$ cover range $[1, X_i]$
and part of optimal solution $t_1,\ldots,t_i$ cover range $[1, T_i]$
then $T_i \le X_i$.

Proof:
By induction:
- For $i = 1$ we have $T_1 = X_1 = R_1$ (the
first chosen segment will be the same)
- Suppose it holds for $i = k-1$ then $T_{k-1} \ge X_{k-1}$.
  The set of all possible next segment is all segments $j$
  having $L_j \le X_{k_1}$. This set will also include segment $t_k$
  as $L_{t_k} \le T_{k-1}$.
  Since we choose the segment with maximum $R_j$ out of all possible
  segments, it will hold that $R_{x_k} \ge R_{t_k}$ and subsequently
  $T_k \ge X_k$.

By this claim, after choosing $P$ segments using the greedy strategy,
we will have $X_P \ge T_P$ which means $X_P \ge M$.

**Variants**

- Finding union of set of segments

## Job deadline problem

There are $N$ jobs to be completed.
Initially time $T$ is $T = 0$.
$i$-th job takes $a_i$ time to complete and
has deadline $T = b_i$.

Is there a way to complete all jobs within time limit?

**Solution**

It is quite intuitive that we take up jobs with earliest deadlines first.

For some arbitrary time $T = d$, all jobs with deadline $b_i \le d$
must be completed. This requires, for such jobs: $\sum{a_i} \le d$.

This leads to the following:
If we order the jobs in non-decreasing order of deadlines
(assume, for convenience, that input is given in this way),
then for each job $k$ it should hold that:

$$
\left(\sum_{1 \le i \le k}{a_i}\right) \le b_k
$$

If it doesn't hold for each $k$, then it is not possible to complete
all jobs within time.

If it does, then the same order is the solution.

## Interval Scheduling

Given a set of intervals, find the largest subset of non
intersecting intervals, i.e, no two intervals in that subset
intersect.

#### Solution

Let interval $i$ be defined by left and right ends $(l_i, r_i)$.

Sort the intervals by $r_i$ and
traverse the intervals from $1$ to $n$, selecting
any interval which is non intersecting with
the current selection.

Proof: Renumber the intervals $1$ to $n$ after sorting.
Let $f_i$ denote maximum number of non-intersecting intervals
from among intervals $1$ to $i$. Then,
- $f_1 = 1$
- $f_i = \max(f_{i-1}, \max \{ f(j) \vert (j < i) \land (r_j \lt l_i)\} + 1)$

It is clear that $f_i$ is non-decreasing and $f_n$ is the solution.

Now we show that the given definition of $f$ can be achieved by
the greedy strategy mentioned above.
For $f_1$ it holds true.
Suppose that for $f_1, \ldots, f_{i-1}$ it holds true.
Now, if interval $i$ does not intersect with the
current selection then we have $f_i = f_{i-1} + 1$ which
is the maximum we can achieve.

However, if interval $i$ intersects with the
current selection then we get $f_i = f_{i-1}$.
This is the maximum value because as we move
back i.e, for $j = i-1,i-2,\ldots, 1$ checking $r_j < l_i$,
we lose the intervals we have selected.
Assume that on reaching some $j$, no interval is
lost (implying $f_{i-1} = f_{j}$) and $r_j < l_i$,
we supposedly get a better answer $f_j + 1 =
f_{i-1} + 1$. But this means that interval $i$ did
not intersect with the current selection
since the selection till $f_{i-1}$ is same as selection
till $f_{j}$ contradicting the assumption.

***