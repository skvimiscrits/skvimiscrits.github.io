---
layout: page
title: Binary representation
parent: Programming
math: true
nav_order: 1
---

## Integer representation

This is an introductory section about integer representation
in binary form.

In most programming languages, an integer
is stored in two's complement form. This form is described below.

### One's complement 

One's complement is an operation which inverts all the bits.
For example, one's complement of `00011001` is `11100110`.

### Two's complement

Two's complement of a $n$-bit integer $x$ is the integer $y$
such that $x + y = 2^n$.

Operation wise, we first invert the bits and then add $1$
For example, two's complement of `00011001` is `11100111`.

### Representing negative numbers

If we have $n$ bits to represent a non-negative integer
we can have $[0, 2^n - 1]$.

But for negative integers, we need some way to store
that extra information (that the number is negative).
There are multiple ways:

#### Sign-magnitude form

One bit is used to denote sign and rest of the bits
to denote magnitude. Using $n$ bits in total, 
magnitude is in range $[0, 2^{n-1} - 1]$ (because we
only have $n-1$ bits for it). Let $M = 2^{n-1} - 1$, then
we can represent integers in range $[-M, M]$.

#### One's complement system

If leftmost bit (MSB) is $1$, the stored integer
is negative and take one's complement to get the magnitude.

Let $M = 2^{n-1} - 1$.
Using $n$ bits we can represent $[-M, M]$.

To illustrate with $n = 16$:
- maximum would be `01111111 11111111`
- minimum would be `10000000 00000000`
  - it will be converted to `01111111 11111111` on one's complement

#### Two's complement system

If leftmost bit (MSB) is $1$, the stored integer
is negative and take two's complement to get the magnitude.

Using $n$ bits we can represent $[-2^{n-1}, 2^{n-1} - 1]$

Example with $n = 16$:
- maximum would be `01111111 11111111`
- minimum would be `10000000 00000000`
  - it will be converted to `10000000 00000000` on two's complement

### Related problems

#### Gray code sequences

Sequence of $2^n$ integers containing all $n$ bit distinct integers with adjacent
elements of sequence differing by exactly $1$ bit (also holds for first and last one).

Example: for $n = 3$:
```
000
001
011
010
110
111
101
100
```

Basic idea is: if have a $2^n$ gray code sequence, can you make a $2^{n+1}$ length one?

Let the $S_n$ be a $2^{n}$ length gray code sequence.
- Let $P$ be $S_n$ with added MSB 0 (the $(n+1)$ th bit).
- Let $Q$ be be $S_n$ **reversed** with added MSB 1

The solution is $P + Q$.

Explanation:
- Let $x$ and $y$ be first and last elements of $P$.
- Then $y'$ and $x'$ are the first and last elements of $Q$. Here $t'$ denotes $t$ with MSB 1 added.
- $y$ and $y'$ have 1 bit difference.
- $x'$ and $x$ have 1 bit difference.

***