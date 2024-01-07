---
layout: page
title: Basics - 2
parent: Math
math: true
nav_order: 2
---

## Base / radix

Base / radix is the number of unique digits used to represent a number.

> In the following discussion, base 10 / decimal is used as default
> representation of values.

For base $k$ numbers, we can use digits $0, \ldots, k-1$.

> The representation might differ from actual value. For
> example in base 16 (hexadecimal) 'a' to 'f' are used for 10 to 15

If a natural number $x$ has digits $x_0, \ldots, x_n$ ($x_i$ is $i$-th digit, least to most significant) 
in base $k$ representation, then it holds that:

$$
x = x_0 k^0 + x_1 k^1 + \ldots + x_n k^n
$$

Also, for all $i$, $0 \le x_i \lt k$

We can add an arbitrary number of zeros to $x$ without changing the value but
those are not shown for obvious reasons.

For example if $x = 174$ in base 8 (octal), then its decimal equivalent will be:

$$
4 \cdot 8^0 + 7 \cdot 8^1 + 1 \cdot 8^2 = 4 + 56 + 64 = 124
$$

### Uniqueness of representation

For a given base, every number has a unique representation.

To prove it, we first show (for some base $k$):
- Maximum value represented using $n$ digits is
  - $(k-1)k^0 + (k-1)k^1 + \ldots + (k-1)k^n$
  - $= (k-1)(k^0 + k^1 + \ldots + k^n)$
  - $= (k^{n+1} - 1)$

- Minimum non-zero value represented using only $(n+1)$-th place is $1 \cdot k^{n+1} = k^{n+1}$.

Now suppose some number has two representations
in base $k$ ($n$ is maximum non-zero digit place from $x$ and $y$):
- $x = x_0, x_1, \ldots, x_n$
- $y = y_0, y_1, \ldots, y_n$

Starting from $n$ down to $0$, suppose $x$ and $y$ differ at $t$
and assume $x_t \gt y_t$ which means that this place introduces
a difference of at least $k^t$ which has to be compensated by $t-1$
digits of $y$. But maximum value that can be represented by $t-1$ digits
is $k^t - 1$.

Since this contradicts that $x = y$, there will be no such $t$, i.e., representations have to be same.

### Minimum powers summation

For some natural number $x$, what is the minimum number of terms whose
summation is $x$ where each term is a power of $k$?

For example:
- $132 = 10^2 + 10^1 + 10^1 + 10^1 + 10^0 + 10^0$ (6 terms)
- $132 = \underbrace{10^1 \cdots 10^1}_{\text{13 times}} + 10^0 + 10^0$ (15 terms)

**Claim**: Unique base $k$ representation of a number $x$ also gives minimum
number of powers of $k$ which sum up to $x$

**Proof**

Some natural number $x$ in base $k$ representation can be written in
sum form as $x = x_0 k^0 + x_1 k^1 + \ldots x_n k^n$ where $0 \le x_i \lt k$.

We can also say $x$ is summation of $x_0$ terms of $k^0$, $x_1$ terms of $k^1$ and so on.
This leads to a total of $x_0 + x_1 + \ldots + x_n$ terms.

Suppose this is not the optimal solution and the optimal solution is given
by $x = y_0 k^0 + \ldots + y_m k^m$ i.e, number of terms is $y_0 + \ldots + y_m$. Now,
- If for some $i$ we have $y_i \ge k$, then we can combine $k$ instances of these
  to form a higher power of $k$. But this can't be the case since this is the optimal solution.
- Otherwise it will hold for all $i$ ($0 \le i \le m$) that $y_i \lt k$ then
  either we have a new base $k$ representation of $x$ or $x_i = y_i$ for all $i$.

Thus $x_i = y_i$ for all $i$.

$\blacksquare$

## Binary exponentiation

### Way 1
Basically $a^b = (a^{b/2})^2$ if $b$ is even
and $a^b = a^{b-1} \cdot a$ otherwise

Thus we compute in $O(\lg b)$

### Way 2

Base 2 breakdown of $b$:

$b = b_0 2^0 + b_1 2^1 + \ldots + b_k 2^k$

Then,
$a^b = a^{b_0 2^0} \cdot \ldots \cdot a^{b_k 2^k}$

Now the trick here is that:

$a^{2^{x+1}} = (a^{2^x})^2$

We can simply iterate over each
power starting from 0 and use this computation.

```
ans = 1, x = a
for i = 0 to K
  if a & (1 << i)
    ans = ans * x
  x = x * x
```

## Recurrence relations

### Example: Fibonacci series

Fibonacci series is a famous series given by recurrence
relation:

$$
F_n = \begin{cases}
  0 & \text{if } n = 0 \\
  1 & \text{if } n = 1 \\
  F_{n-1} + F_{n-2} & \text{otherwise}
\end{cases}
$$

TODO: Golden ratio $\phi = \frac{1 + \sqrt{5}}{2}$.

> Binet's Fibonacci number formula:
> $$
> F_n = \frac{(\phi^n - (-\phi)^{-n})}{\sqrt{5}}
> $$

### Generating function


$$
G(x) = \sum_{0 \le i \le \infty}{f(i) \cdot x^i}
$$

**Example**

If we have recurrence relation $f(n + 1) = 2 f(n) + 3$ with $f(0) = 0$

Then,

$G(x) = f(0) \cdot x^0 + \sum_{i}{(2 f(i) + 3)x^{i+1}}$

$G(x) = 2xG(x) + 3x\sum_{i}{x^i}$

$(1-2x)G(x) = \frac{3x}{1-x}$ (We take $x \lt 1$)

$G(x) = \frac{3x}{(1-x)(1-2x)}$

$G(x) = 3(\frac{1}{1-2x} - \frac{1}{1-x})$

$G(x) = 3(\sum_{i}{2^i x^i} - \sum_{i}{x^i})$

$G(x) = \sum_{i}{3(2^i-1) x^i}$

$f(n) = 3(2^n - 1)$

### Matrix representations

TODO

***