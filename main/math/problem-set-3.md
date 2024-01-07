---
layout: page
title: Problem set - 3
parent: Math
math: true
nav_order: 12
---

#### Problem

Given integers $a$ and $b$,
divide $a$ by $b$ without using division, multipliciation, mod
and report answer without fractional part (truncate towards $0$)

> The required division is similar to C++ division. Note that it
> is different from floor for negative results.

#### Solution

Let us first consider case of $a \ge 0$ and $b \gt 0$.
We can express $a$ in terms of $b$:

$a = bk + r$ where $k, r$ are integers and $0 \le r \lt b$.

We need to find the maximum $k$ such that $bk \le a$.
Considering $k$ as $2^{x_0} + 2^{x_1} + \ldots + 2^{x_m}$,
we will check bits from highest down to lowest positions whether
a bit can be set.

Sample implementation:
```
i = 32, k = 0
while (i >= 0):
  if (a >= b<<i):
    a -= b<<i
    k += 1<<i
  i--
output k
```

#### Problem

[Original problem](https://codeforces.com/contest/1114/problem/E)

An arithmetic progression $x_1, \ldots, x_n$ with common difference $d > 0$ is
permuted into sequence $a_1, \ldots, a_n$ which is hidden from us.

We can perform $Q$ queries in total of form:
- Given $i$, get value of $a_i$
- Given $k$, reports whether there exists an element greater than $k$.

We need to find $x_1$ and $d$.

Constraints:
- $1 \le n \le 10^6$
- $0 \le x_i \le 10^9$ 
- $Q = 60$

#### Solution

It is fairly obvious that we can obtain the maximum element,
$x_n$, in no more than $30$ queries of type 2.

How can we obtain the value of $d$ using $30$ remaining queries,
on upto $10^6$ elements?

We will now describe a randomized solution:
- Select $i$ uniformly randomly from $[1, n]$ and use type 1 query to get $a_i$.
- Let $a_i = x_j$. Two cases are possible: $x_n - a_i = 2pd$ or $x_n - a_i = (2q + 1)d$.
- If we obtain $m$ such indices $i_1, \ldots, i_m$ and corresponding
  values $y_j = x_n - a_{i_j}$, we claim that $d = \gcd(y_1,\ldots,y_m)$

The above claim (without specifying proof) is incorrect with probability
$\approx 2 \cdot 10^{-9}$.

> The explanation for the probability is omitted here. Basically we need
> to find the probability that if we select a length $k$ subsequence
> from elements $1,\ldots,n$ the GCD of differences is $1$.

***