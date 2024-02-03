---
layout: page
title: Bit manipulation - 2
parent: Programming
math: true
nav_order: 3
---

### Bit manipulation related problems

#### Illustration 1

Given $n = 2p + 1$ integers where $p$ integers occur twice,
find the integer which occurs once.

We exploit $x \oplus x = 0$.

The answer is given by $a_1 \oplus \ldots \oplus a_n$

#### Illustration 1.1

Given $n = 3p + 1$ integers where $p$ occur thrice, find
the one which occurs once.

A simple way is to use bit frequency. If bit $i$ occurs
$k$ times, then either $k \mod 3$ is $0$ or $1$. If it is
once, then this bit $i$ is set in the result.

> What if negative integers are there?
> - The bit representation will still be same
>   for two negative integers with equal value.
> - Need to be careful while comparison. 
>   Checking `(x & (1 << i)) > 0` can fail if `i`
>   is the leftmost bit (as it is used for negative numbers in C/C++, two's complement form).
>   Instead, checking `(x & (1 << i)) != 0` is correct

A fancier solution involves deriving a formula.

For each bit position, we need to know whether it is
of form $3k$ or $3k+1$. Or if $x$ is the number of
1-bits, we need to know $x \bmod 3$.

We can have 3 states of a bit: $0, 1, 2$.
Each state has name corresponding to number of bits
recorded (modulo $3$).

Initially state is $0$. Transitions on getting a 1-bit are:
- $0 \rightarrow 1$
- $1 \rightarrow 2$
- $2 \rightarrow 0$

To store $3$ states, we need at least $2$ bits. 

It is easy to process these states individually for each
bit, say using an array.

But can we do it combined?
If we use two integers $a, b$ such that for a
bit position $p$, bits of $a, b$ at same position give state ($0, 1, 2$).
We can consider $a$ to hold bit for $2^0$ and $b$ to hold $2^1$.

Now, these transitions in form of $a, b$ become:

|$x$| $b,a$ | $b',a'$ |
|---|---|---|
|0|00 | 00|
|0|01 | 01|
|0|10 | 10|
|1|00 | 01|
|1|01 | 10|
|1|10 | 00|

K-Map for output $a'$:

||00|01|11|10|
|---|---|---|---|---|
|0|0|1|x|0|
|1|1|0|x|0|

$a' = \overline{x}\overline{b}a + x\overline{b}\overline{a}$

$a' = \overline{b}(x \oplus a)$

K-Map for output $b'$:

||00|01|11|10|
|---|---|---|---|---|
|0|0|0|x|1|
|1|0|1|x|0|

$b' = \overline{x}b + xa$

At the end of all transitions, $a$ will hold the
answer as $a = 1$ means $(3k+1)$ 1-bits and $a = 0$
means $3k$ bits.

#### Illustration 1.2

From $n = 2p$ integers, all except two numbers $a, b$ where $a \neq b$ occur twice.
Find $a$ and $b$.

**Solution**

Let the target numbers be $a, b$

Observations:
- XOR sum of all numbers will be $a \oplus b$
- $a \oplus b \neq 0$ as $a \neq b$
- Let $a_i \oplus b_i = 1$ ($i$-th bit)
  - There will be at least one such bit
- Partition all numbers based on $i$-th bit into two groups:
  - All pairs will be in the same group
  - $a$ and $b$ will not be in same group
  - Individual XOR sum of both groups will be the answer.



#### Illustration 2

Find maximum value of $u \text{ or } v$ (bitwise) where $L \le u, v \le R$.

Constraints:
- $1 \le L \le R \le 10^{18}$

*Solution*

Assume $n$ bits in total.
Also, assume $u \ge v$.

Suppose $k$ MSB bits of $L$ and $R$ are same.
These bits will not change in the answer. Either $L = R$ or
the next bit (let it be bit $i$) would be $1$ in $R$ and $0$ in $L$. Let $u = L$ and $v = R$.
- In $u$ we will set all bits $j$ such that $j \lt i$ as $1$

$u$ will still be in bounds $[L, R]$ and $v = R$ will give maximum answer.

#### Illustration 2.1

Find maximum value of $u \oplus v$ (bitwise) where $L \le u, v \le R$.

Constraints:
- $1 \le L \le R \le 10^{18}$

*Solution*

Suppose $k$ MSB bits in $L$ and $R$ are same. These can never
change in the selected numbers.
If $L \neq R$, then next bit (let it be position $i$) will be $0$ in $L$ while being $1$ in $R$.
Let $u = L$ and $v = R$.
- In $u$ we will set all bits $j$ such that $j \lt i$ as $1$.
- In $v$ we will set all bits $j$ such that $j \lt i$ as $0$.

Both $u, v$ will still be in bounds $[L, R]$ and result into
maximum answer.

#### Illustration 2.2

Find number of integers $x$ satisfying
$x \oplus N \ge N$ in range $[L, R]$

Constraints:
- $1 \le N, L, R \le 10^{18}$
- $L \le R$

*Solution*

Let $m$ be the highest bit position. We will
start constructing $x$ from bit $m$. Based on
the choice of bit $m$ in $x$ we can have these cases:
- $x_m \oplus N_m < N_m$ : We will ignore this case as further bits won't change this inequality.
  - This will be the case when $N_m = 1$ and $x_m = 1$
- $x_m \oplus N_m = N_m$ : We will analyze further bits
  - This will be the case when $N_m = 0$ or $1$ and $x_m = 0$
- $x_m \oplus N_m > N_m$ : All combinations of further bits will not change this
  inequality. Hence we will count all cases.
  - This will be the case when $N_m = 0$ and $x_m = 1$

Following the second case, suppose we are at bit $k$. Then:
- $x_{k+1,m} = 0$ and $x_{k+1,m} \oplus N_{k+1,m} = N_{k+1,m}$
- For $x_k$, we will have three cases as described above.

In the third case, we will be finding range intersection
of $[2^k, 2^{k+1} - 1]$ and $[L, R]$. These cases will be
unique because bit $k$ is always set (and no higher bits are set) in this range.

Time complexity will be $O(m) = O(\lg{N})$

***