---
layout: page
title: Problem set - 1
parent: Math
math: true
nav_order: 10
---

## Floor sum

Let $f$ be defined as (for positive integer $x$)

$$
f(x) = 
\left\lfloor \frac{x}{1} \right\rfloor +
\left\lfloor \frac{x}{2} \right\rfloor +
\ldots +
\left\lfloor \frac{x}{x} \right\rfloor
$$

Compute $f(n)$ where $1 \le n \le 10^9$

**Solution**

First we show that multiset $\{ \left\lfloor \frac{n}{1} \right\rfloor, \ldots, \left\lfloor \frac{n}{n} \right\rfloor\}$
has atmost $2\lfloor \sqrt{n} \rfloor$ distinct integers.

Let $p = \lfloor\sqrt{n}\rfloor$

From $\lfloor \frac{n}{1} \rfloor, \ldots, \lfloor \frac{n}{p} \rfloor$, we get at most $p$ distinct integers.

Let $k = \lfloor \frac{n}{p+1} \rfloor$.
Observe that sequence $\lfloor \frac{n}{p+1} \rfloor, \ldots, \lfloor \frac{n}{n} \rfloor$
- is non-increasing
- ends at $1$, all elements are integers
- If it starts with $k$, it can have at most $k$ distinct integers.

> In fact, this sequence contains exactly $k$ distinct integers $1, \ldots, k$
> which can be proved by analyzing differences of terms.

Let $D$ denote the number of distinct integers, then we have shown that $D \le p + k$

$\Rightarrow D \le \lfloor\sqrt{n}\rfloor + \lfloor \frac{n}{\lfloor\sqrt{n}\rfloor+1} \rfloor$

$\Rightarrow D \le 2\lfloor\sqrt{n}\rfloor$

Also note: largest integer $k$ satisfying $\lfloor \frac{n}{k} \rfloor = x$ $(x \ge 1)$ is
given by $\lfloor \frac{n}{x} \rfloor$, if at least one 
integer satisfies this.

Since $\lfloor \frac{n}{k} \rfloor = x$,

$\Rightarrow \frac{n}{k} \ge x$

$\Rightarrow k \le \frac{n}{x}$

So, largest integer $k = \lfloor \frac{n}{x} \rfloor$

> Note: Example when such integer $k$ doesn't exist:
> $\lfloor \frac{10}{k} \rfloor = 6$

**Algorithm**

- Let $s \leftarrow 0$
- Let $p \leftarrow \lfloor\sqrt{n}\rfloor$
- For $i = 1$ to $p$ do: $s \leftarrow s + \lfloor \frac{n}{i} \rfloor$
- Let $l \leftarrow p + 1$
- While $l \le n$, do:
  - Let $x \leftarrow \lfloor \frac{n}{l} \rfloor$
  - Let $r \leftarrow \lfloor \frac{n}{x} \rfloor$
  - $s \leftarrow s + x(r - l + 1)$
  - $l \leftarrow r + 1$

Notes:
- Here $r$ gives the furthest point in sequence until which the values
	are same. Then since all values from $i = l$ to $r$ are same, we add
	$(r-l+1)x$ to sum.

***