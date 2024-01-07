---
layout: page
title: Problem set - 2
parent: Math
math: true
nav_order: 11
---

#### Problem

We are given integers $a_1,\ldots,a_N$.

Find (any) integer $x$ which minimizes $f(x) = \sum_{1 \le i \le N}{\vert a_i - x\vert}$

#### Solution

*Without proof*

$x = a_k$ where $k = \lceil \frac{N}{2} \rceil$

Note: If $N$ is even, $k = \lceil \frac{N}{2} \rceil + 1$ is also optimal.

Details [here](https://terxor.github.io/cp/main/problem-solving/atcoder-abc-127.html) (Problem F)

#### Problem

Given set $\{1, 2, \ldots, n\}$ with $n \ge 1$
find a way to divide it into two sets such that the difference of absolute sums of the numbers in sets is minimum

For example, for $n = 5$ we can have $\{1, 3, 4\}$ and $\{2, 7\}$ resulting in difference $\vert 8 - 9 \vert = 1$

#### Solution

*Claim:* We can express each of $0, \ldots, \frac{n(n+1)}{2}$ as a sum using numbers, each **at most once**,
from $\{1, 2, \ldots, n\}$.

*Proof*:

Greedy algorithm:
- Let $p \leftarrow x$.
- For $i = n$ down to $1$, if $p \ge i$:
  - output $i$
  - $p \leftarrow p - i$

We need to prove that at the end, $p = 0$

Let us analyse two cases:
- For every iteration, $i$ is subtracted for $p$.
  - This can happen only if $x = \frac{n(n+1)}{2}$
    and $p$ becomes $0$ at the end.
- $k$ is the first iteration where subtraction doesn't occur (i.e, $p \lt k$)
  - If $p$ is not already $0$, then $1 \le p \lt k$
  - Next iteration in which subtraction will take place is $i = p$ in
    which $p$ will become $0$. Iterations after $p = 0$ have no action.

$\blacksquare$

For the problem, if $S_n = \frac{n(n+1)}{2}$ is even, we can form,
by the above claim, $\frac{S_n}{2}$ using some numbers.
The rest of the numbers will also have this sum. The difference will
be $0$.

If $S_n$ is odd, then we can form sum $\lceil \frac{S_n}{2} \rceil$ and
sum of the rest will be $\lfloor \frac{S_n}{2} \rfloor$. The difference
will be $1$.

#### Problem

Given a stick of length $n$ (integer). You can make cuts at integer points
only. Cut this stick into $k$ pieces ($k \le n$) such that $\sum\limits_{1 \le i \le k}{l_i}^2$
is minimized (where $l_i$ is length of piece $i$)

#### Solution

TODO

#### Problem

Given numbers $a$ and $b$ where $a$ can be a huge number,
find the suffixes and prefixes of $a$ divisible by $b$.

We have $1 \le a \le 10^{100000}$ and $1 \le b \le 10^9$.

#### Solution

Let $a$ be $a_0,a_1,\ldots,a_n$ (least to most significant)

Let $a_{i, j}$ denote number represented by $a_i,\ldots,a_j$
(least to most significant)

Let $P_i = a_{0,i} \mod b$

Then $P_{i+1} = (P_i + a_{i+1} \cdot 10^{i+1}) \mod b$

Let $S_i = a_{n,i} \mod b$.

Then $S_{i-1} = (S_i \cdot 10 + a_{i-1}) \mod b$

#### Problem

Given integers $a_1, \ldots, a_n$ and $k$, find largest positive integer $p$
such that $a_i \bmod p = k$ for all $i$ ($1 \le i \le n$).

Constraints:
- $1 \le n \le 10^5$
- $1 \le k, a_i \le 10^9$

#### Solution

If any of $a_i \lt k$, then will be no solution. We will assume, $a_i \ge k$.

If $a_i \bmod p = k$, then:
- Let $b_i = a_i - k$
- $b_i \bmod p = 0$
- If $b_i \le b_j$ then $b_j - b_i = cp$ where $c \ge 0$

The answer will be GCD of differences of adjacent elements $b$ arranged in ascending order.

#### Problem

*Find max gcd after subtracting same number from array of integers*

Given integers $a_1, \ldots, a_n$, find largest positive integer $p$ such that
there exists some $k$ satisfying $a_i \bmod p = k$ for all $i$ ($1 \le i \le n$).

Constraints:
- $1 \le n \le 10^5$
- $1 \le a_i \le 10^9$

#### Solution

Assume $p$ is the optimal solution.
We have $a_i \bmod p = k$ ($k$ not known to us)

$\Rightarrow a_i = p x + k$

Let $d_i = a_{i+1} - a_{i}$ (assume $a$ sorted)

then $d_i = p x_{i+1} - p x_i = p (x_{i+1} - x_i)$

Thus, $\gcd(d_1, \ldots, d_{n-1}) = p$

> Note: If by this way $p = 0$, all numbers will be equal and this
> case can be handled separately.

#### Problem

*Mininum number with GCD > 1*

Given $N$, find the minimum number $X$ such that
- $\gcd(i, X) > 1$ ($\forall$ $1 \lt i \le N$)

#### Solution

For each prime $P$, $\gcd(P, X) = kP$ which means
$X$ should have $P$ as a factor

This means that for each prime $P_i \le N$, $X$ should have it as
a factor.

Thus, $X = P_1 \cdot P_2 \cdot \ldots \cdot P_K$ (where $P_K$ is the
largest prime not greater than $N$)

#### Problem
*Find maximum integer solution for quadratic within a range*

TODO: Explain

```cpp
long calc (long a, long b, long c, long mx) {
  // ax^2 + bx + c -> max integral value in range 1...mx
  auto f = [&] (long x) { return a * x * x + b * x + c; };
  auto adjust = [&] (long& x) {
    if (x < 1) x = 1;
    if (x > mx) x = mx;
  };
  if (a >= 0) return max(f(1), f(mx));
  /* find extremum -> 2ax + b = 0 -> x = -b/2a */
  double ex = -(double)b / (2 * a);
  long x1 = (long)floor(ex);
  long x2 = (long)ceil(ex);
  adjust(x1);
  adjust(x2);
  return max(f(x1), f(x2));
}
```

#### Problem

Find highest power of $a$ which divides $n!$ where:
- $1 \le n \le 10^{18}$
- $2 \le a \le 10^{12}$

#### Solution

For a prime $p$, $p^k$ divides $n!$ if $p$ occurs at least $k$ times
in prime factorisation of $n!$.

We can either find highest power of $p$ in each term in $[1, 2, \ldots, n]$
or we can find number of terms
in $[1, 2, \ldots, n]$ which are divisible by $p, p^2, \ldots, p^k$.

Second approach is easier in this case because
number of terms in $[1, 2, \ldots, n]$ divisible $p^k$ is simply given by $\lfloor \frac{n}{p^k} \rfloor$

Highest power of $p$ divisble is given by $f(p) = \sum\limits_{1 \le i \lt \infty} \lfloor \frac{n}{p^k} \rfloor$

Note that for each power we add only single instance
because $p^{i}$ adds $i$ powers but this was also counted in $p^{i-1}$.

To check for $a$, let $p_1^{t_1}, \ldots, p_m^{t_m}$ be prime factorization of $a$.

For each prime, we get max allowed power $\lfloor \frac{f(p_i)}{t_i} \rfloor$.
The answer is minimum over max allowed powers for each prime.

#### Problem

*Floor ceil decomposition*

You start with (multi) set $S = \{x\}$

In one operation, you can replace some number $u$ in $S$
by two numbers $\lfloor \frac{u}{2} \rfloor$ and $\lceil \frac{u}{2} \rceil$.

Find the maximum possible product of all numbers in $S$ after
any number of operations.

Constraints:
- $1 \le x \le 10^{18}$

#### Solution

It is always beneficial to decompose some $u \ge 4$ into
$\lfloor \frac{u}{2} \rfloor$ and $\lceil \frac{u}{2} \rceil$
as the product will be greater than or equal to $4$.

So, if $x \gt 1$, we will always end up with possibly multiple $2$ and $3$
in $S$.

To get the final number of instances of $2$ and $3$ in $S$, we will
need to decompose $x$. It might seems that it can be decomposed to
huge number of values with the given bound.

But it turns out that if you have $\{u, u + 1\}$ in $S$ ($u \ge 4$),
decomposing both will lead to some $\{v, v + 1\}$ (possibly multiple instances)

We will consider two cases:
- $u = 2k$, then $2k$ will decompose to $k, k$ and $2k+1$ will decompose
  to $k,k+1$.
- $u = 2k+1$, then $2k+1$ will decompose to $k, k+1$ and $2k+2$ will decompose
  to $k+1,k+1$.

Thus, we will never have more than $2$ distinct numbers in $S$.
Overall, there will be $O(\lg{x})$ distinct numbers in $S$.

We can solve this in $O(\lg{x})$.

#### Problem

*Tags: Primes, factorization*

You will be given $n$. Find a sequence of positive integers
$a_1,\ldots,a_n$ which satisfies the following:
- For each $i, j$ such that $1 \le i \lt j \le n$, if $i$ divides $j$
then $a_i \neq a_j$
- $\max{(a_i)}$ is minimum possible

#### Ideas

Maybe it can be solved by some graph algorithm but intended solution is
around factorization/primes/divisors.

For some $x$, let the prime factorization be $x = \prod\limits_{1 \le i \le k}{p_i}^{q_i}$

Consider this sequence of numbers:

$$
b = [1, p_1, p_1^2, \ldots, p_1^{q_1}, p_1^{q_1} p_2,\ldots,p_1^{q_1} p_2^{q_2}
\ldots, x]
$$

Here we start from $1$ and keep on multiplying the primes factors that make up $x$.
It holds that:
- $b$ has $\sum{q_i} + 1$ elements.
- $b_i$ divides $b_j$ for some $i \lt j$

Thus all of the elements in $b$ (taken as indices in $a$, i.e., $a_{b_1}, a_{b_2},\ldots$)
must have different colors.

This gives us a lower bound for number of colors required.
Let $f(x)$ denote $\sum{q_i} + 1$ where prime factorization of $x$ is as described above.
Then, lower bound is $\max\limits_{1 \le i \le n}{f(i)}$

Interestingly, it turns out that we can use coloring $a_i = f(i)$.
This is because it holds that if $i \lt j$ and $i$ divides $j$ then $f(i) \lt f(j)$
hence the condition will not be violated.

***