---
layout: page
title: Combinatorics - 3
parent: Math
math: true
nav_order: 9
---

## Catalan numbers

Catalan numbers is an important series as it occurs as a solution
for many combinatorics problems.
 First few terms: $1, 1, 2, 5, 14, 42, 132, 429, \ldots$

In general, $n$-term (starting from $n = 0$) of this series is given by:

$$
C_n = \frac{1}{n+1} \cdot \binom{2n}{n}
$$

This is the closed form of the following recursive definition:

$$
C_n = \begin{cases}
1 & \text{if $n = 0$} \\
C_0 C_{n-1} + C_1 C_{n-2} + \ldots + C_{n-1} C_0 & \text{otherwise}
\end{cases}
$$

## Famous related problems

### Unlabelled binary trees

Find the number of different binary trees that can be
formed using $n$ **unlabelled** vertices (i.e., different on basis
of structure)

Let $f_n$ be that number.

Then, $f_0 = 1$ (empty tree)

General case:
Assign any vertex as root. The remaining $n - 1$ vertices can
belong to either left subtree or right subtree. This gives
us recursive definition of $f$:
	
$$
f_n = f_0 f_{n-1} + f_1 f_{n-2} + \ldots + f_{n-1} f_0
$$

We see that $f_n = C_n$.

> Note: Number of *labelled* binary trees are $(n!) \cdot C_n$

### Non intersecting segments in circle

Find the number of ways $n$ segments can be obtained from a circle
having $2n$ points on its circumference **without intersection**.

Let $f_n$ be that number.

Then, $f_0 = 1$ (no points, no segments)

Let the points be labelled $1,\ldots,2n$.

Now in each valid configuration, point $1$ will be paired with some other point.
If point $1$ is paired with point $k$, then we divide the problem
into subproblems of size $k-2$ and $2n-k$ since intersection of segments is not allowed.

But subproblem size has to be even otherwise there will be no solution.
This means $k$ has to be even. Let $k = 2p$, then subproblem sizes
become $2(p-1)$ and $2(n-p)$. We get:

$$
f_n = \sum_{1 \le p \le n}{f_{p-1} f_{n-p}}
$$

It is fairly obvious that $f_n = C_n$.

### Valid parenthesis strings

Find number of valid parenthesis string containing $n$ pairs
of parenthesis (string contains *only* parenthesis symbols)

Let $f_n$ be that number.

Then $f_0 = 0$ (empty string).

To divide this to subproblems, we will focus on the length of
first parenthesized term (up to closing counterpart of opening parenthesis at start of string).
It will be even (because each opening parenthesis will be matched with closing).

The remaining string can be any valid
parenthesis expression. If the first part has length $2k$,
then the number of valid expressions can be $f_{k-1}f_{n-k}$
since first part will have $2(k-1)$ symbols inside it (which also have to form a valid expression)
and second part contains remaining $2(n - k)$ symbols.

Thus,

$$
f_n = \sum_{1 \le i \le n}{f_{i-1}f_{n-i}}
$$

It is clear that $f_n = C_n$.

> Note: We consider combinations of what's *inside* of first part. This is
> to make counting easier.
>
> Suppose we chose to arbitrarily divide the string
> into two valid parenthesized strings, then there would be duplicate counting,
> for example: `()()()` can be divided either like `()(),()` or `(),()()` and both, upon further division,
> can give `(),(),()`.

### Valid parenthesization (in expression)

Find number of ways of valid parenthesization of an expression of $n + 1$ terms.
Valid parenthesization is defined as:
- Single term should not be parenthesized.
- Whole expression should not parenthesized.
- Parentheses are correctly matched.

Let $f_n$ be that number.

Then, $f_0 = 1$ (a single term, with no parenthesis)

To get a recurrence relation for $f_n$, we split the expression in two parts
with left one having $k$ terms and the right one having $n - k + 1$ terms ($k$ can be $1, \ldots, n$)
and parenthesize the left part (except when it is a single term). Here we use number of terms in left expression only to avoid duplicate counting.

If right part has more than one term, then we have a choice to
put a parentheses around it (or not).

So, for some $k$, number of ways are $f_k (f_{n-k+1} + f_{n-k+1}) = 2 f_k f_{n-k+1}$.
Here $f_{n-k+1}$ added twice represents the choice of putting parenthesis around
second term (in both cases number of ways will be same).

Thus,

$$
f_n = 2 f_0 f_{n-1} + 2 f_1 f_{n-2} + \ldots + 2 f_{n-1} f_0
$$

This looks similar to recurrence relation of $C_n$ but not same.

> In fact, the series resulting from this recurrence is called **super catalan numbers**.

### Valid parenthesization (in expression) with binary operator

Find the number of ways in which $n + 1$ factors can be parenthesized
such that each multiplication is between 2 terms (for example $a \times b \times c$ is wrong as multiplication is among three terms)

Let $f_n$ be that number.
Then $f_0 = 1$ (single term).

Similar to valid parenthesization in expression,
split the expression in two parts, one with $k$ terms and other with $n - k + 1$
terms.

We parenthesize *both* parts, if they contain more than one term (as operator is binary), getting:

$$
f_n = f_0 f_{n-1} + f_1 f_{n-2} + \ldots + f_{n-1} f_0
$$

We get $f_n = C_n$.

***