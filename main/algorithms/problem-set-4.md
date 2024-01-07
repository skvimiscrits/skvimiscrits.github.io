---
layout: page
title: Problem set - 4
parent: Algorithms
math: true
nav_order: 14
---

## Load balancing 1

You have $n$ bags and $m$ balls.
Ball $i$ has weight $a_i$.
You need to to keep each ball in some bag.
In one bag you can keep at most $k$ balls.

After keeping all balls:
- Let $w_i$ be the weight of bag $i$.
- Let $\mu$ be the average weight of all bags.

Minimize:

$$
\sum\limits_{1 \le i \le n}{\vert w_i - \mu \vert}
$$

Constraints:
- $1 \le n, m \le 10^5$
- $1 \le a_i \le 10^9$
- $m \le n \cdot k$

#### Solution

TODO

## Coin change - Minimum selection

We have $n$ types of coins with values $c_1, \ldots, c_n$ (all positive distinct integers)

Given $x$, find the minimum number of coins that can make up that value.

> NOTE: Specific variants:
> - Minimum coins that make up a value with coin values 1,2,5,10,20,100
> - Minimum number of perfect squares that make up a number
>
> How to decide when greedy algorithm will work?

#### Solution

TODO

## Coin change - Max distinct

We have $n$ types of coins.
Coin type $i$ has value $c_i$.
Also, it holds that:
- $c_1 = 1$
- $c_i \lt c_{i+1}$ for all $1 \le i \lt n$

For some amount $x$, we define greedy selection of coins as:
Until $x = 0$, select largest value coin not exceeding $x$ and subtract its
value from $x$.

Let $f(x)$ be the number of distinct type coins obtained by greedy selection.

Let $M$ be the maximum value of $f(x)$ over all non-negative integers.
Find smallest $x$ such that $f(x) = M$.

#### Solution

TODO

***