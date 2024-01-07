---
layout: page
title: Basics - 3
parent: Math
math: true
nav_order: 3
---

## Iterated functions and cycle detection

A function $f: X \rightarrow X$, when composed to itself $n$ times,
it is called $n$-th iterate of $f$ and it can be denoted by $f^n(x)$.

For example, $f^3(x) = f(f(f(x)))$.

Applying $f$ again on a result can be called 'iterating'.

### On finite sets

For a function $f: X \rightarrow X$, let $n = \vert X \vert$.

Then, it holds that if we start from some value $x \in X$
and repeatedly iterate through $f$, we will obtain some value $y$
which occurs at least twice in **not more than** $n$ iterations.

This is because we start from a single value $x$ and iterate $n$ times,
thus obtaining $n+1$ values (including $x$). From a total of $n+1$ values, at least two must be
same as $n$ is the set size.

For example, let $X = \{1, 2, 3, 4\}$ and $f$ be defined as:

|$x$|$f(x)$|
|---|---|
|$1$|$2$|
|$2$|$3$|
|$3$|$4$|
|$4$|$2$|

Suppose we start from $x = 1$:
- $f(1) = 2$
- $f(2) = 3$
- $f(3) = 4$
- $f(4) = 2$, here $2$ repeats.

Suppose some $x$ occurs on first on $i$-th iteration and then
on $j$-th iteration ($i \lt j$). Then it holds that
$x$ will occur every $(j - i)$ iterations as we will be
getting the same sequence of values over and over again.

This is called a **cycle**. The number of iterations over which
values repeat is called **cycle length**.

> Finding a cycle depends on the starting point.
> 
> Also, there can be multiple cycles in a function. Example:
>
> |$x$|$f(x)$|
> |---|---|
> |$1$|$2$|
> |$2$|$1$|
> |$3$|$4$|
> |$4$|$3$|
>
> Here $1, 2$ form a cycle and $3, 4$ form another cycle.

### Cycle detection problem

Let $f: X \rightarrow X$ be a function on finite set $X$.

For a starting value $x_0$, find start $\mu$ of the cycle and
length $\lambda$ of that cycle.

In other words, let $x_i$ be $f^{i}(x_0)$ ($f$ applied $i$ times). We need to find smallest
$(u, v)$ such that $x_u = x_v$ ($u \lt v$). Here,
- $\mu = x_u$ is start of the cycle
- $\lambda = v - u$ is the cycle length

### Floyd's hare and tortoise algorithm

Assume that the sequence $x_0, x_1, \ldots$ is represented
by a directed graph (with a cycle).

In this algorithm, hare and tortoise are initially on
node $x_0$.

A *step* here means moving to another node using the only outgoing edge from current node.

There are multiple turns. In each turn, these actions take place
sequentially:
- Firstly, hare moves two steps.
- Then, tortoise moves one step.

After some $k$ ($k > 0$) turns, hare and tortoise
will be on the same node. That node will be a part of the cycle.
This can be shown by two observations:
- Hare and tortoise will both reach the cycle eventually and will never leave it.
- Hare has to cross the tortoise in some turn (as they are in a cycle with hare moving twice as fast). If not already on same
  node before the turn, hare will be one node ahead after its move. Then tortoise will
  move one step and both will be on same node.

At this point, after $k$ turns, hare has taken $2k$ steps and tortoise has taken $k$ steps.
Since they are at same point, this implies that if tortoise moves $k$ more steps, it will reach same point.
Thus, cycle length $\lambda$ divides $k$. 

Now, if we place the tortoise at the start again and in each turn **both** move one step each, then (at end of some turn) they will meet
for the first time at the start of the cycle.

This can be shown by observations:
- They will always meet: after $k$ steps, tortoise will reach the point
  at which they met in initial scenario. Since $k$ is a multiple of cycle length,
  hare will be at the same point.
- This requires them to be at the same point when tortoise enters the cycle.
- There onwards they are always at same point at each step.

We can also prove this by contradiction: Suppose they do not meet
after the turn where tortoise enters the cycle, then they will never meet after any number of turns (as they move one step each).

We can summarize the algorithm:
- Place hare and tortoise on start, move until they meet at some node $x$ of cycle (in $k$ moves).
- Place tortoise on start again and move until they meet. This node will be $\mu$ (starting point).
- Move either tortoise or hare a number of times until it reaches $\mu$ again. The number
  of times moved is cycle length $\lambda$.

Sample implementation:
```py
def calc(f, start):
	# Part 1: Reach common point
    h, t = f(f(start)), f(start)
    while h != t:
        h, t = f(f(h)), f(t)
	
	# Part 2: Find cycle start
    t = start
    while h != t:
        h, t = f(h), f(t)
    cycle_start = t

	# Part 3: Find cycle length
    t, len = f(t), 1
    while t != h:
        t = f(t)
        len += 1
    return (cycle_start, len)
```

***