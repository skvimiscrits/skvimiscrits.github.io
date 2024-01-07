---
layout: page
title: Analysis of algorithms
parent: Algorithms
math: true
nav_order: 1
---

## Measuring efficiency

To analyse the efficiency of an algorithm, we need to analyse two factors:
- Time required for the algorithm to complete.
- Space requirements throughout the execution.

> Space usage is usually put in terms of maximum space used throughout the
> execution of the program. This is because a program can request for more
> space or *free* the space used.

While analysing the running time, we are (usually) not interested in exact
running time. Rather, we want to know how the running time grows as the input
size grows.

Suppose we have two algorithms $A$ and $B$ to solve a given problem with
running times $T_A(n) = 2n^2$ and $T_B(n) = 100n$ then even though $A$ will run
very quickly compared to $B$ for small values of $n$, it will have very poor
performance for large $n$. For instance, $T_A(1000) = 2 \cdot 10^6$ but
$T_B(1000) = 1 \cdot 10^5$ which means $B$ runs $20$ times faster than $A$ for
$n = 1000$ and it will be much more faster for even larger values of $n$.

## Asymptotic Notation

As it may be evident from the example above, we are more concerned with the
*order of growth* or *asymptotic efficiency* of an algorithm rather than the
exact running time. Using **asymptotic notation** for expressing running time
makes it more convenient to compare efficiency of algorithms because it hides
less useful information (such as constants and lower order terms).

Suppose for some algorithm we have $T(n) = n^2 + 100n$. Here the running time
consists of two terms: $n^2$ and $100n$. The second term is insignificant in
the analysis of growth of $T(n)$ because even though it might contribute
significantly for small values of $n$, its contribution is essentially nothing
for large $n$ (say $n = 10^4$, then the first term will be $10^8$ while the
second term will be $10^6$ which is $1\%$ of the first term)

The functions used in the definition of asymptotic notation have the set of
natural numbers as the domain and they are asymptotically non-negative. A
function $f(n)$ is asymptotically negative if there exists some $n_0$ such that
$f(n) \geq 0\ \forall\ n \geq n_0$

### $\Theta$-Notation

For a function $g(n)$, we define $\Theta(g(n))$ as a set of functions. A
function $f(n) \in \Theta(g(n))$ if there exist positive constants $c_1$, $c_2$
and $n_0$ such that

$$ c_1 g(n) \leq f(n) \leq c_2 g(n) \ \forall\ n \geq n_0 $$

To indicate that a function $f(n)$ is a member of $\Theta(g(n))$, it not
uncommon to see $f(n) = \Theta(g(n))$ being used instead of $f(n) \in
\Theta(g(n))$ (also applicable to other asymptotic notations)

If $f(n) = \Theta(g(n))$, we say that $g(n)$ is an *asymptotically tight bound*
for $f(n)$

### O-Notation

$$ O(g(n)) = \{ f(n) \mid \exists\ c, n_0 \gt 0 \text{ s.t. } 0 \le f(n) \le c
g(n)\ \forall\ n \ge n_0 \} $$

If $f(n) = O(g(n))$, we say that $g(n)$ is an *asymptotically upper bound* for
$f(n)$

### $\Omega$-Notation

$$ \Omega(g(n)) = \{ f(n) \mid \exists\ c, n_0 \gt 0 \text{ s.t. } 0 \le c g(n)
\le f(n)\ \forall\ n \ge n_0 \} $$

If $f(n) = \Omega(g(n))$, we say that $g(n)$ is an *asymptotically lower bound*
for $f(n)$

By the definitions of the notations above, it becomes easy to prove the
following theorem: *For any two functions $f(n)$ and $g(n)$, we have $f(n) =
\Theta(g(n))$ if and only if $f(n) = O(g(n))$ and $f(n) = \Omega(g(n))$*

This theorem makes it clear that $\Theta$ is a stronger notation than $O$ or
$\Omega$

## Dependence of running time on input

The running time of an algorithm may also depend on the actual input along with
the input size.

For example, the Insertion sort (more details in a later section) runs fast on
already sorted input and runs slow on reverse sorted input. Thus, it might be
the case that there does not exist an asymptotically tight bound to the running
time in general (independent of the input, dependent on the input size).
However, the asympotically lower and upper bounds always exist:
- The asymptotic lower bound of the running time in general is same as that of
  the best case (input for which algorithm runs fastest)
- The asymptotic upper bound of the running time in general is same as that of
  the worst case (input for which algorithm runs slowest)

Back to the Insertion sort example:
- The running time to sort $n$ elements in the best case is $T_{\text{best}}(n)
  = \Theta(n)$ which implies $T_{\text{best}}(n) = O(n)$ and
  $T_{\text{best}}(n) = \Omega(n)$.
- For the worst case $T_{\text{worst}}(n) = \Theta(n^2)$ which implies
  $T_{\text{worst}}(n) = O(n^2)$ and $T_{\text{worst}}(n) = \Omega(n^2)$.

The only conclusion we can make about the running time $T(n)$ in general is
$T(n) = \Omega(n)$ and $T(n) = O(n^2)$. Usually we are interested only in the
upper bound of running time of an algorithm which explains why running times
(in general) are expressed in $O$-notation.

### Constant time/space fallacy

Suppose for an algorithm, the maximum input size $n = 100$.  Further let the
running time of this algoritm be $T(n) = 2n$, which implies $T(n) = O(n)$.

Suppose we modify our algorithm to always work with $n = 100$ even when actual
input size is lesser (say by using dummy values).  Now, let the modified
running time be $T'(n) = 200$.  Then, $T'(n) \ge T(n)$ for any valid value of
$n$ (since $n \le 100$).  It also holds that $T'(n) = O(1)$ meaning that it
runs in *constant time*.

To avoid such analysis, we should assume that maximum input size is not known
while analyzing time or space complexity.

Another example: we are asked to find number of set bits in an $n$-bit integer
with constraints $1 \le n \le 32$. Now, C/C++ provide `int` type which is a
32-bit integer.  If we write an algorithm which iterates over the $n$-bits and
computes the answer, will it be $O(1)$ or $O(n)$? One can argue that since bits
of order higher than $n$ will always be unset and we can iterate over all 32
bits resulting in a $O(1)$ algorithm.

In conclusion, theoretical analysis should be helpful in correctly predicting
the behaviour of algorithm based on input size.  Constraints on input size
should be considered *afterwards*.

## More on asymptotic notations

These notations are not commonly used.

### $\omega$ and $o$ notations

$o$-notation denotes an upper bound which is not asymptotically tight. In other
words, it is a set of functions which grow faster than the given function. For
example: $g(n) = n^2$ is an asymptotically upper bound for function $f(n) = 2
n^2$ but $f(n) \neq o(g(n))$ because it is asymptotically tight. However, $h(n)
= n^3$ is an asymptotically upper bound which is not asymptotically tight. We
have $f(n) = o(h(n))$.

The $\omega$-notation is just the opposite of $o$-notation. It denotes a lower
bound which is not asymptotically tight.

Mathematically,

$$ o(g(n)) = \{ f(n) \mid \exists\ n_0 \gt 0 \text{ s.t. } 0 \le f(n) \lt c
g(n)\ \forall\ n \ge n_0, c \gt 0 \} $$

If $f(n) = o(g(n))$ then we have $\lim_{n\to\infty}\frac{f(n)}{g(n)} = 0$

$$ \omega(g(n)) = \{ f(n) \mid \exists\ n_0 \gt 0 \text{ s.t. } 0 \le c g(n)
\lt f(n)\ \forall\ n \ge n_0, c \gt 0 \} $$

If $f(n) = \omega(g(n))$ then we have $\lim_{n\to\infty}\frac{g(n)}{f(n)} = 0$

$f(n) = \Theta(g(n)) \Rightarrow f(n) = O(g(n))$ but $f(n) = \Theta(g(n))
\nRightarrow f(n) = o(g(n))$. Also, $f(n) = o(g(n)) \Rightarrow f(n) =
O(g(n))$.

### Some properties Some of the properties of asymptotic notation are listed
here:

- Transitivity

  $$ f(n) = \Theta(g(n)) \text{ and }  g(n) = \Theta(h(n)) \Rightarrow f(n) =
  \Theta(h(n)) $$ This also holds true for $O$, $\Omega$, $o$ and $\omega$
  notations.

- Reflexivity
  - $f(n) = \Theta(f(n))$
  - $f(n) = O(f(n))$
  - $f(n) = \Omega(f(n))$ 

- Symmetry

  $f(n) = \Theta(g(n)) \Leftrightarrow g(n) = \Theta(f(n))$

- Transpose symmetry
  - $f(n) = O(g(n)) \Leftrightarrow g(n) = \Omega(f(n))$
  - $f(n) = o(g(n)) \Leftrightarrow g(n) = \omega(f(n))$

***
