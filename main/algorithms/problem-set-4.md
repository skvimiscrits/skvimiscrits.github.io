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

## More problems

### Equalizing by adjacent moves

We have $n$ boxes where box $i$ has $a_i$ balls.

One move consists of:
- Select a subset of boxes having at least one ball
- Take them out of the box
- For each ball, place it in any box adjacent to its original one

Find the minimum number of moves to make the number of balls equal in each box.

The constraints guarantee that it is possible.

Constraints:
- $1 \le n \le 10^5$
- $0 \le a_i \le 10^5$
- $\sum{a_i} = kn$ where $k$ is some positive integer

#### Solution

Imagine a junction between box $i$ and $i+1$.
In one move at most one ball can pass through it.

Since we know the final number of ball in each box,
we can calculate how many balls will
pass through each junction

Let $f_i$ be the number of balls that need to pass
from box $i$ to box $i+1$.
and $g_i$ be the number of balls that need to pass
from box $i$ to box $i-1$.
For convenience:
- Let $f_n = g_1 = 0$
- Let $S_{l,r} = \sum\limits_{l \le i \le r}{a_i}$.

Then,

$$
f_i = \max(0, S_{1, i} - ik) \\
g_i = \max(0, S_{i, n} - (n - i + 1)k)
$$

For box $i$, define the total number of balls that have
to go out as $h_i$ which given by $h_i = f_i + g_i$. Since only one ball can go out of
a single box in one move, we have lower bound for the
solution as $\max\limits_{1 \le i \le n}{h_i}$

We will show a way to achieve the lower bound:

- Consider the box for which $h_i$ is maximum ($h_i \gt 0$)
- This box will have at least one ball. We will show this:
  - Suppose the box has $0$ balls. In such a case, either
    $f_i = 0$ or $g_i = 0$ (because only one of the sides
    will have deficit and other will have surplus).
  - Suppose $g_i \gt 0$ then $f_i = 0$, then balls in range $[1, i-1]$
    have deficit, move to box $i+1$ (it has to exist), it will
    hold that $g_{i+1} = g_i + k$. Thus, $h_{i+1} \gt h_i$ contradicting
    that we chose maximum. We can show a similar argument for $f_i \gt 0$.
- Also note, for boxes $i$ and $i+1$ it can never be the
  case that $f_i \gt 0$ and $g_{i+1} \gt 0$ because
  $f_i \gt 0$ indicates deficit in range $[i+1, n]$.

Strategy to achieve lower bound:
- Choose boxes having maximum value of $h$.
  For such a box $i$, if $f_i \gt 0$, pass one ball to box $i+1$
  otherwise pass one ball to box $i-1$. It will have at least
  one ball as shown above.
- After we do this for all such boxes, the maximum value of $h$
  reduces by $1$.

Hence, we can equalize the balls in $\max{h_i}$ moves.

***