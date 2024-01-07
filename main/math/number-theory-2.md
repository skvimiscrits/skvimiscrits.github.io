---
layout: page
title: Number theory - 2
parent: Math
math: true
nav_order: 6
---

## Linear Diophantine Equations

The equation $ax + by = c$ where $a,b,c$ are given integers and only
integer solutions are accepted is called a *linear Diophantine equation*.

Such an equation has no solution if $c$ is not a multiple of
$\gcd(a, b)$. The reason is simple: if $g = \gcd(a, b)$ then
$a = a'g, b= b'g$ and $ax + by$ is $g(a'x + b'y)$.

Otherwise, it has infinitely many solutions. Suppose $(x, y)$
is a solution. Then $(x + kb', y - ka')$ is also a solution ($k$ integer).

But if we want to show that infinitely many solutions always exists when
$c$ is a multiple of $\gcd(a, b)$, we have to show at least one solution exists.

### Bezout's identity

> Let $a$ and $b$ be integers with greatest common divisor $d$. Then there exist integers $x$ and $y$ such that $ax + by = d$.
> 
> Moreover, the integers of the form $az + bt$ are exactly the multiples of $d$. 

To prove Bezout's identity, we simply have to show one such $(x,y)$.

Recall **Euclidean algorithm**.
Assume $a \le b$.

$$
\gcd(a, b) =
\begin{cases}
b & \text{if } a = 0 \\
\gcd(b \pmod a, a) & \text{otherwise}
\end{cases}
$$

We need to find $(x, y)$ such that $ax + by = d$ where $d = \gcd(a, b)$.

Let 
- $a' = b \pmod a = b - ka$ (in fact $k = \lfloor \frac{b}{a} \rfloor$)
- $b' = a$.

Then, $\gcd(a', b') = d$ from the definition.

Now assume that we have a solution for $a'x + b'y = d$ as $(x', y')$

$\Rightarrow a'x' + b'y' = d$

$\Rightarrow (b - ka)x' + ay' = d$

$\Rightarrow a(y' - kx') + bx' = d$

Thus, we can obtain the solution recursively, just like we obtain
GCD.

We can now define a method $G$ such that $G(a, b)$ will return
a triple $(d, x, y)$ where $d = \gcd(a, b)$ and $(x, y)$ is a solution
to $ax + by = d$.

Algorithm which computes $G$ is called **Extended Euclidean algorithm**.

Sample implementation:

```py
def gcd_ext(a, b):
    if a == 0: return (b, 0, 1)
    (d, a1, b1) = gcd_ext(b % a, a)
    return (d, b1 - (b // a) * a1, a1)
```

## Modular multiplicative inverse

- How is Extended Euclidean algorithm used for calculation modular multiplicative inverse?

- If $P$ is a prime, what is $\frac{1}{B} \pmod P$?


Methods of computing inverse

```cpp
inv[1] = 1;
for (int i = 2; i < N; i++) {
  inv[i] = Mod - (Mod / i) * inv[Mod % i] % Mod;
}
```

***