---
layout: page
title: Number theory - 1
parent: Math
math: true
nav_order: 5
---

## Prime numbers

An integer $p$ is a prime number if
- $p > 1$
- No integer $q$ such that $q > 1$ and $q \neq p$ divides $p$

For example: $2, 3, 5, 7, 11\ldots$

Number of prime numbers less than $10^x$:

| $x$ | $10^x$     | # of primes |
| --- | ---------- | ----------- |
| 1   | 10         | 4           |
| 2   | 100        | 25          |
| 3   | 1,000      | 168         |
| 4   | 10,000     | 1,229       |
| 5   | 100,000    | 9,592       |
| 6   | 1,000,000  | 78,498      |
| 7   | 10,000,000 | 664,579     |

### Some interesting results:

- Let $\pi(n)$ : Number of prime numbers $\leq n$
  
  Then, $\pi(n) = O(\frac{n}{ln(n) - 1})$

- Sum of reciprocals of primes up to $n$ is $\approx \ln{\ln{n}}$

- **Prime gap** refers to the number of numbers between two consecutive
  primes and it is useful sometimes.
  Here are some upper bounds on prime gaps in ranges:
     
  | Range            | Upper bound on gap |
  | ---------------- | ----------- |
  | $[1, 10^3]$      | $22$        |
  | $[1, 10^6]$      | $118$       |
  | $[1, 10^9]$      | $288$       |
  | $[1, 10^{12}]$   | $582$       |
  | $[1, 10^{15}]$   | $916$       |
  | $[1, 10^{18}]$   | $1476$      |

### Finding primes - Seive of Eratothenes

To find all primes upto $N$:
1. Write down integers from $2$ to $N$
1. Let $x = 2$
1. If $x$ is crossed out, go to step 5.
1. Cross out all numbers divisible by $x$ excluding $x$.
1. If $x < N$, set $x \leftarrow x + 1$ and go to step 3.
1. Numbers which are not crossed out are primes.

which can be implemented as:
```cpp
const int N = 10000;
bitset<N> isPrime;
vector<int> primes;

void sieve() {
  isPrime.set();
  isPrime[0] = isPrime[1] = false;
  for (int i = 2; i < N; i++) if (isPrime[i]) {
    primes.push_back(i);
    for (int j = i * i; j < N; j += i) isPrime[j] = false;
  }
}
```

Time complexity:

Let $P$ be the set of primes less than or equal to $N$.
Then
- The outer loop only runs $\vert P \vert$ times.
- The inner loop runs $\frac{N}{p}$ times for $i = p$

Thus, we have time complexity:

$$
\sum_{p \in P} \frac{N}{p}
= N \cdot \sum_{p \in P} \frac{1}{p}
\approx O(N \lg{\lg{N}})
$$

> Here we have used the result of sum of reciprocals of primes

### Factors / Divisors

> - A positive integer $x$ is called a factor/divisor of positive integer $y$
>   if $x \vert y$ ($x$ divides $y$).
> 
> - A factor/divisor which is a prime number is called a prime factor or
>   prime divisor.
> 
> - Expressing a positive integer as a product of its prime factors is
>   called prime factorization.

#### Factorization by trivial method

Finds prime factors $N$ by iteratively checking divisibility by smaller
integers (starting from $2$) and dividing $N$ if divisible.

Makes use of these observations about smallest factor $p$ ($p \ge 2$) of
integer $N$ ($N \ge 2$):
- $p$ is a prime 
- Either $p \le \sqrt N$ or $p = N$ (in that case $N$ is a prime)

```cpp
vector<int> factorize(int n) {
  vector<int> res;
  for (int i = 2; i * i <= n; i++) {
    while (n % i == 0) {
      res.push_back(i);
      n /= i;
    }
  }
  if (n > 1) res.push_back(n);
  return res;
}
```

Time complexity is $O(\sqrt N)$

This method can be optimized by checking only odd numbers. But we need to check $2$ as a special case.

#### Using primes generated by sieve

Suppose we have generated all primes $\le M$. Then, we can
factorize any $N \le M$ faster than trivial method.

```cpp
vector<int> primes; // assume populated

vector<int> factorize(int n) {
  vector<int> res;
  for (int i = 0; primes[i] * primes[i] <= n; i++) {
    while (n % primes[i] == 0) {
      res.push_back(primes[i]);
      n /= primes[i];
    }
  }
  if (n > 1) res.push_back(n);
  return res;
}
```

Time Complexity: $O(\frac{\sqrt N}{\lg{N}})$

> *Todo: How did we get this result*

## Sieve variants

Classic sieve can be modified to compute, for example, sum
of prime factors.

In all such variants we compute a function $f$ for integers $1$ to $M$
(where $M$ is *typically* in order of $10^5$).

For classic sieve, $f(x) = 1$ if $x$ is a prime, $0$ otherwise.
Implementation wise, an array is used for $f$. We may sometimes use notation $f_x$ to denote $f(x)$.

Common variants:
- Smallest and largest prime factors
- Number of distinct prime factors
- Sum of prime factors
- Number of divisors
- Sum of divisors
- Euler's totient function

### Smallest and largest prime factors

Define $f_x$ to be the smallest prime factor of $x$.

1. Set $f_x \leftarrow x$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x < x$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow \min(x, f_y)$
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

Upon reaching some $x$, computation of $f_x$ is always complete. 
Thus we can identify if it is a prime number 
by checking $f_x < x$ (as done in step 3) because any prime factor of non-prime is smaller than it.

In step 4, for a prime $x$, we update its multiples $y$. 
If instead of $f_y \leftarrow \min(x, f_y)$ we do $f_y \leftarrow x$ then we
will have $f_x$ as largest prime factor of $x$. The reason is simple: any non-prime
will be last updated by its largest prime factor.

### Number of distinct prime factors

Define $f_x$ to be the number of distinct prime factors of $x$.

1. Set $f_x \leftarrow 0$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x > 0$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow f_y + 1$
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

Upon reaching some $x$, we can identify if it is a prime number 
by checking $f_x = 0$ (as done in step 3) because otherwise its prime factors would have
updated it in step 4 or earlier iteration(s) (as it would obviously be a multiple of its prime factors).

Step 4 is inituitive because for each prime, we add $1$ to all of its multiples
increasing the distinct prime factor count.

### Sum of prime factors

Define $f_x$ to be the sum of prime factors of $x$.

1. Set $f_x \leftarrow 0$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x > 0$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow f_y + x$
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

This is very similar to the previous variant. The only difference
here is that instead of $f$ denoting count, it denotes sum which explains
why we have $f_y \leftarrow f_y + x$ instead of $f_y \leftarrow f_y + 1$.

### Number of divisors

> *A recap on calculating number of divisors*
>
> If prime factorisation of some $N$ is
> 
> $$
> N = p_1^{q_1} \cdot \ldots \cdot p_K^{q_K}
> $$
> 
> where $p_1,\ldots,p_K$ are distinct prime factors of $N$ and
> $q_1,\ldots,q_K$ are their respective powers,
> then the number of divisors of $N$ is
> 
> $$
> (1 + q_1) \cdot (1 + q_2) \cdot \ldots \cdot (1 + q_K)
> $$
> 
> Reason being, from each prime factor we can take $0, 1, 2 ... q_i$ instances of it.


Define $f_x$ to be the number of divisors of $x$.

1. Set $f_x \leftarrow 1$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x > 1$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow f_y \cdot (k + 1)$
   where $k$ is the highest power of $x$ which divides $y$.
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

This is, again, similar to the previous variants. Here, in step 4, we compute and multiply $f_y$ with
the number of instances of prime $x$ occuring in $y$, plus one (relate to $q_i$ and product of $1 + q_i$ above).

### Sum of divisors

> *A recap on calculating sum of divisors*
>
> If prime factorisation of some $N$ is
>
> $$
> N = p_1^{q_1} \cdot \ldots \cdot p_K^{q_K}
> $$
> 
> Similar to number of divisors, sum of divisors can be expressed as:
> 
> $$
> (p_1^0 + \ldots + p_1^{q_1}) \cdot (p_2^0 + \ldots + p_2^{q_2}) \cdot \ldots \cdot (p_K^0 + \ldots + p_K^{q_K})
> $$

Define $f_x$ to be the sum of divisors of $x$.

1. Set $f_x \leftarrow 1$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x > 1$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow f_y \cdot (1 + x^2 + \ldots x^k)$
   where $k$ is the highest power of $x$ which divides $y$.
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

Here, in step 4, we compute and multiply $f_y$ with
the sum of all possible powers of $x$ occuring in $y$ (refer to expression of
sum of divisors above).

### Euler's Totient function

> Euler's totient function or $\Phi(n)$  function gives the count
> of positive integers less than $n$ which are coprime to $n$.
> 
> Positive integers $a$ and $b$ are said to be coprime if $\gcd(a, b) = 1$.
> In other words, they have no common factor other than 1.
>
> Euler's product formula is helpful in computing $\Phi(n)$:
>
> $$
> \Phi(n) = n \cdot (1 - \frac{1}{p_1}) \cdot \ldots \cdot (1 - \frac{1}{p_K})
> $$
> 
> where $p_1, \ldots, p_K$ are distinct prime factors of $n$.

Define $f_x$ as $f_x = \Phi(x)$.

1. Set $f_x \leftarrow x$ for $x = 2,\ldots,M$.
1. Let $x = 2$
1. If $f_x > 1$ then $x$ is not a prime. Go to step 5.
1. For all positive multiples $y$ ($y \le M$) of $x$ do $f_y \leftarrow f_y \cdot (1 - \frac{1}{x})$
1. $x \leftarrow x + 1$
1. If $x \le M$ go to step 3.

Here, in step 4, we multiply $f_y$ with
a term similar to that in Euler's product formula.

### Segmented Sieve - Primes in a range

Classic sieve can find only small primes (in range $2,\ldots,M$ with $M$ of order $10^6$)

To find primes in a given range $[L, U]$ where $L$ and $U$ are large but $\sqrt U$ and $U-L$ are small,
we can use this method:

1. Obtain a list $P$ of primes not greater than $\sqrt U$
1. Let $f_x \leftarrow 1$ for $x = L,\ldots,U$.
1. For each prime $p \in P$, for all multiples $x = k \cdot p$ ($k > 1$) such that $L \le x \le U$ do $f_x \leftarrow 0$

It is based on this observation: if a number $x$ in this range is non-prime, then
it must have a prime factor $y$ such that $y \le \sqrt U$. 

> $L = 1$ is an edge-case which is not handled here

Time complexity is similar to that of classic sieve:
- For each prime $p$, we have to do $\frac{U - L}{p}$ operations.
- In total we have:
  $$
  \sum_{p \in P}\frac{U-L}{p} = (U - L) \cdot \lg\lg(\sqrt U)
  $$
  since $P$ contains primes upto $\sqrt U$. We have used the result of
  sum of reciprocal of primes here.

***
