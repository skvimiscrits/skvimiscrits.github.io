---
layout: page
title: Problem set - 2
parent: Algorithms
math: true
nav_order: 12
---

## Smallest subsequence of fixed length

Given a sequence of integers $a_1,\ldots,a_n$, and an integer $k$ ($1 \le k \le n$),
find the *smallest* subsequence of $a$ having length $k$.

A sequence $p_1,\ldots,p_m$ is smaller than $q_1,\ldots,q_m$ if there exists $i$
such that $p_j = q_j$ $\forall$ $j \lt i$ and $p_i \lt q_i$.

From all such smallest sequences, the sequence formed by the indices must also be smallest.
This ensures a unique solution.

### Solution

First element can be chosen from anywhere but last $k-1$ indices.

In general, let $t$ ($t_1,\ldots,t_k$) be the solution. Then it holds for every $i$:
- $t_i \le n - k + i$

Solution can be found using a greedy algorithm:
1. Let $x = \min\limits_{1 \le i \le n - k + 1}{a_i}$
1. Let $p$ be the index of earliest occurence of $x$.
1. $t_1 \leftarrow p$
1. If $k = 0$ then stop.
1. Reduce to subproblem $k \leftarrow k-1$ and $a \leftarrow a_{p+1},\ldots,a_n$

Above solution can be directly implemented in $O(n^2)$.
It can be reduced to $O(n\lg{n})$ by means of static range minimum query.

#### Further ideas

An important observation is described below:

Let $t_1,\ldots,t_k$ (indices) be the optimal solution.

- From the range limits above: $t_i \le n-k+i$
- If for some $i$, $t_i = n-k+i$ then for all $j \ge i$ it will hold that $t_j = n-k+j$.
- Let $t_p$ be the last element such that $t_p \lt n-k+p$, then $a_{t_1},\ldots,a_{t_p}$
  form a non-decreasing sequence.

The last point can be proved as follows:

Suppose $a_{t_i} \gt a_{t_{i+1}}$

Since it holds that $t_{i+1} \lt n - k + i + 1$

$\Rightarrow t_{i+1} \le n - k + i$ 

This means that $t_{i+1}$ is a valid and better choice for $i$-th element than $t_i$
which contradicts that the solution is optimal.

This shows that the optimal solution is formed by a non-decreasing sequence
followed by a contiguous sequence of elements at the end.

We now give an algorithm to find optimal solution $t$.

```
Input: a[1...n], k

let t = []
for i = 1 to n
  while t.size > 0 and a[t.last] > a[i] and t.size >= max(0, i-n+k)
    t.pop()
  if t.size < k
    t.append(i)
return t
```

The condition `t.size >= max(0, i-n+k)` ensures that we
have taken the minimum required number of elements till index `i`.
- If the loop breaks due to this condition, `i` is added in the next
  step (because `t.size = max(0, i-n+k)-1 < k`). Hence this will hold true at the end of each iteration.

Let `m = t.size` for some `i`, then we can show that
`t` will contain the smallest sequence of size `m` in range of indices `[1, i]`.
Let's call this `p`.

Suppose we have the smallest sequence `q` such that `a[q[j]] < a[p[j]]` for some `j`.
- If `q[j] < p[j]`, then `q[j]` will be inserted into `t` in iteration `i = q[j]`
  (conditions `t.size = k` and `a[t.last] <= a[q[j]]` are not possible since `q` is the smallest).
  Now, in the iteration `i = p[j]`, element `q[j]` inserted earlier will never be removed.
- If `q[j] > p[j]`, then `p[j]` will be inserted first. Simply put, `j` elements
  are sufficient till index `q[j]` (because a solution exists), hence `p[j]` will be replaced by `q[j]`
  because `p[j]` was `j`-th element.

### Related problem 1

Given two sequences $a$ and $b$ of lengths $n$ and $m$ respectively,
find the smallest subsequence of length $k$ ($k \le n + m$) formed as a result
of combining two subsequences of $a$ and $b$.

#### Solution

Suppose the optimal solution is formed by $p$ and $q$ elements ($p + q = k$) from $a$ and $b$ respectively.

It is obvious that these subsequences will be smallest among sequences of same size independently.
We can use the above algorithm in obtain the independent
subsequences in $O(n + m)$.

Now, we have to merge these sequences optimally.

#### Subproblem

Merge two sequences $a$ and $b$ of lengths $n$ and $m$ to
form the *smallest* sequence.

It is obvious that we will take the smaller element first.

But when $a_i = b_j$, we should take the element which leads
to a better choice later. Suppose $a_{i+t} = b_{i+t}$
for $t = 1,\ldots,p-1$ and $a_{i+p} \neq b_{i+p}$ then
we have to make the decision according to this comparison.

This takes $O(nm)$ time (loose upper bound).

#### Overall solution

We will consider each combination of $p$ and $q$ such that $p+q=k$
and compute the smallest sequence for each. There will be $k$ such sequences.
We will then return the smallest of these.

Overall running time is $O(k n m)$ (loose upper bound).

## Median of two sorted lists

We are given two integer lists $a$ (length $n$) and $b$ (length $m$)
which are sorted in ascending order.

Find the median of $a, b$ merged.

**Solution 1** - Binary search on median value

Time complexity: $O(\lg M \cdot \lg nm)$
where $M$ be the range size of values that elements
of $a$ and $b$ can take.

We can define median of a list of integers $l$ of size $n$
(assume $n$ odd):

> Median is the smallest element $x$ such that there are
> at least $\lfloor \frac{n}{2} \rfloor + 1$ elements lesser
> than or equal to $x$ in $l$

When $n$ is even, the definition changes to:

> Median is $\frac{x+y}{2}$ where
> $x$ is the smallest element such that there are
> at least $\lfloor \frac{n}{2} \rfloor + 1$ elements lesser
> than or equal to $x$ in $l$
> and $y$ is the smallest element such that there are
> at least $\lfloor \frac{n}{2} \rfloor$ elements lesser
> than or equal to $y$ in $l$

For some $x$, we can find the number of elements
lesser than or equal to $x$ in both lists combined.
This can be done by binary search on both lists
in $O(\lg{n} + \lg{m})$ time.

We can, then binary search on $x$ in possible range
(maximum and minimum elements of $a, b$) and use the above
definition of median.

**Solution 2** - Binary search on number of elements

Time complexity: $O(\lg n \lg m)$

Let's take $n$ as odd first.

Let $x$ be the smallest element in $a$ such that
there are at least $\lfloor \frac{n}{2} \rfloor + 1$ elements
smaller than $x$ in $a$ and $b$ combined.

We can find $x$ by binary search on $a$ (includes binary search on $b$ in each step).
This will take $O(\lg{n}\lg{m})$ time.

Similarly, we will find $y$ in $b$ as defined above.

Then, minimum of $x, y$ is the median. This is because we can get,
say $x \gt y$ when actual median $y$ was not present in $a$.

The case for $n$ being even can be handled similarly.

Sample code:
```cpp
// number of a_i <= k
int f (vector<int>& a, int k) {
    return upper_bound(a.begin(), a.end(), k) - a.begin();
}

// smallest element x in a, b combined having at least k elements <= x
int g (vector<int>& a, vector<int>& b, int k) {
    
    int lo = 0, hi = a.size();
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        int other = f(b, a[mid]);
        if (mid + 1 + other >= k) hi = mid;
        else lo = mid + 1;
    }
    int x = (lo == a.size() ? 1e7 : a[lo]);

    lo = 0; hi = b.size();
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        int other = f(a, b[mid]);
        if (mid + 1 + other >= k) hi = mid;
        else lo = mid + 1;
    }
    int y = (lo == b.size() ? 1e7 : b[lo]);
    return min(x, y);
}

double calc (vector<int>& a, vector<int>& b, int n, int m) {
    int t = n + m;
    if (t & 1) {
        return g(a, b, t / 2 + 1);
    } else {
        double x = g(a, b, t / 2);
        double y = g(a, b, t / 2 + 1);
        return (x + y) / 2;
    }
}
```

**Solution 3** - Binary search on elements of smaller list

Time complexity: $O(\lg(\min(n, m)))$

We will assume $n \le m$ (that is, $a$ is not the longer list)

Further assume $n \gt 0$.

Let $k = \lfloor \frac{n+m}{2} \rfloor + 1$

We will try to find $i, j$ such that:
- $i + j = k$
- $a_i \le b_j$
- $i$ is maximum possible.

Note that this property is binary-searchable.

> For values of $i = 1$ to $n$, we will
> get $j$ in a valid range $j = k - 1$ to $k - n$
> as $k - 1 \le m$ and $k - n \ge 1$.

*Edge case*

The only case where no such $i$ will exist is when $a_1 \gt b_{k-1}$.
In that case, we know the $k-1$ smallest elements of $a, b$ combined.
The next element can be $a_1$ or $b_k$ (if $k \le m$).
We get:
- $(k-1)$-th element as $b_{k-1}$
- $k$-th element as $a_1$ or $\min(a_1, b_k)$ if $k \le m$.

In case of odd, we simply return median as $k$-th element.
In case of even, we return average of $k$-th and $(k-1)$-th element.

*Normal case*

We will obtain $i$ by binary search on $a$
and $j$ simply as $k-i$.

Assume that $i+1 \le n$ and $j - 1 \ge 1$.
We list some properties:
- $a_i \le b_j$ (by definition of $i$ above)
- $a_{i+1} \gt b_{j-1}$ (directly follows by definition of $i$ above)
- There are at least $k$ elements greater than or equal to $a_{i+1}$:
  - $a_1, \ldots, a_{i+1}$ and $b_1, \ldots, b_{j-1}$
  - Thus we don't need to look beyond index $i+1$ in $a$ for median.
- There are at least $k$ elements greater than or equal to $b_j$:
  - $a_1, \ldots, a_i$ and $b_1, \ldots, b_j$
  - Thus we don't need to look beyond index $j$ in $b$ for median.
- Median is contained within $a_1,\ldots,a_{i+1}$
  and $b_1,\ldots,b_j$. But these are $k+1$ elements.
  The greatest among these can be $a_{i+1}$ or $b_j$.
  - $\min(a_{i+1}, b_j)$ is $k$-th
    and $\max(a_i, b_{j-1})$ will be the $(k-1)$-th

The cases where $i + 1 \gt n$ or $j - 1 \lt 1$ can
be handled by simply not considering $a_{i+1}$ and $b_{j-1}$.

Sample implementation:
```cpp
double findMedianSortedArrays(vector<int>& a, vector<int>& b) {
    int n = a.size(), m = b.size();
    if (n > m) {
        swap(n, m);
        swap(a, b);
    }
    if (n == 0) {
        double ans = b[m / 2];
        if (m % 2 == 0) ans = (ans + b[m / 2 - 1]) / 2;
        return ans;
    }
    auto median = [&] (int len, int u, int v) {
        double ans = v;
        if (len % 2 == 0) ans = (ans + u) / 2;
        return ans;
    };
    int k = (n + m) / 2 + 1;
    if (a[0] > b[k - 2]) {
        int t = a[0];
        if (k - 1 < m && b[k - 1] < a[0]) t = b[k - 1];
        return median(n + m, b[k - 2], t);
    }
    int lo = 0, hi = n - 1;
    while (lo < hi) {
        int mid = (lo + hi - 1) / 2 + 1;
        if (a[mid] <= b[k - 2 - mid]) lo = mid;
        else hi = mid - 1;
    }
    int i = lo, j = k - 2 - lo;
    int u = a[i], v = b[j];
    if (j - 1 >= 0) u = max(u, b[j - 1]);
    if (i + 1 < n) v = min(v, a[i + 1]);
    return median(n + m, u, v);
}
```

***