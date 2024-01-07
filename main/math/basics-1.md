---
layout: page
title: Basics - 1
parent: Math
math: true
nav_order: 1
---

### Ranges

Notations:
- $[a, b]$ denotes a range from $a$ to $b$ with both $a$ and $b$ included.
- $[a, b)$ - similar with $b$ excluded.
- $(a, b]$ - similar with $a$ excluded.
- $(a, b)$ - similar with both $a$ and $b$ excluded.

Other
- If there are $n$ points on a number line at $1, 2, \ldots, n$ there
  are $n - 1$ segments of length $1$ between them.
  - Distance point $i$ and point $j$ is $j - i$
  - Number of points between $i$ and $j$ (both inclusive) are $j - i + 1$

### Indices

If there are $N$ elements in a sequence numbered $1$ to $N$.
- There are $j-i+1$ elements between element $i$ and $j$ ($i \ge j$), both inclusive.
- $k$-th element from the end has index $N-k+1$
- If $N$ is odd, the middle element has index $\lceil \frac{N}{2} \rceil$
- If $N$ is even, the two middle elements have indices
  $\lceil \frac{N}{2} \rceil$ and $\lceil \frac{N}{2} \rceil + 1$



### Inequalities

- Largest integer $x$ satisfying $x \le k$ ($k$ can be real) is $\lfloor k \rfloor$
- Largest integer $x$ satisfying $x \lt k$ ($k$ can be real) is $\lceil k \rceil - 1$
- Smallest integer $x$ satisfying $x \ge k$ ($k$ can be real) is $\lceil k \rceil$
- Smallest integer $x$ satisfying $x \gt k$ ($k$ can be real) is $\lfloor k \rfloor + 1$

### Sets

Simplest counting principle: for two disjoint sets $A$ and $B$:

$$
\vert A \cup B \vert = \vert A \vert + \vert B \vert
$$

In general, we should exclude the intersecting part because it gets
included twice:

$$
\vert A \cup B \vert = \vert A \vert + \vert B \vert - \vert A \cap B \vert
$$

Size of union of three sets $A, B, C$ is given by:

$$
\vert A \cup B \cup C \vert = \vert A \cup B \vert + \vert B \vert - \vert (A \cup B) \cap C \vert \\
= \vert A \cup B \vert + \vert B \vert - \vert (A \cap C) \cup (B \cap C) \vert \\
= \vert A \cup B \vert + \vert B \vert - \vert A \cap C \vert - \vert B \cap C \vert + \vert (A \cap C) \cap (B \cap C) \vert \\
= \vert A \vert + \vert B \vert + \vert C \vert - \vert A \cap B \vert - \vert A \cap C \vert - \vert B \cap C \vert + \vert A \cap B \cap C \vert
$$

In general, size of union of $n$ sets $A_1, \ldots, A_n$

$$
\vert A_1 \cup \ldots \cup A_n \vert = \vert (A_1 \cup \ldots \cup A_{n-1}) \cup A_n \vert \\
= \vert A_n \vert + \vert A_1 \cup \ldots \cup A_{n-1} \vert - \vert A_n \cap (A_1 \cup \ldots \cup A_{n-1}) \vert \\
= \vert A_n \vert + \vert A_1 \cup \ldots \cup A_{n-1} \vert - \vert (A_n \cap A_1) \cup \ldots (A_n \cap A_{n-1}) \vert
$$

This is an inductive definition (size of union of $n$ sets in terms
of union of $n-1$ sets). The simplified form is:

$$
\vert A_1 \cup \ldots \cup A_n \vert = \\
  \sum{\vert A_i \vert} - \sum{\vert A_i \cap A_j \vert} + \sum{\vert A_i \cap A_j \cap A_k \vert} + \ldots
$$

This formula is called **principal of inclusion and exclusion**.

See [this](https://math.libretexts.org/Bookshelves/Combinatorics_and_Discrete_Mathematics/Combinatorics_Through_Guided_Discovery_(Bogart)/05:_The_Principle_of_Inclusion_and_Exclusion/5.01:_The_Size_of_a_Union_of_Sets)
for reference.

### Summations

$$
\sum_{1 \le i \le n}{i} = 1 + 2 + \ldots + n = \frac{n (n + 1)}{2}
$$

$$
\sum_{1 \le i \le n}{i^2} = 1^2 + 2^2 + \ldots + n^2 = \frac{n (n + 1) (2n + 1)}{6}
$$

> Derivation:
> 
> Using $i^2 = 1 + 3 + \ldots + (2i - 1)$, we can write:
> 
> - $1^2 = 1$
> - $2^2 = 1 + 3$
> - $3^2 = 1 + 3 + 5$
> - $\ldots$
> - $n^2 = 1 + 3 + 5 + \ldots + (2n - 1)$
> 
> Summation of terms at each position:
> 
> $$
> S = \sum_{1 \le i \le n}{(n - i + 1)(2i - 1)}
> $$
> 
> $$
> S = \sum{-2i^2 + (2n + 3)i - (n + 1)}
> $$
> 
> $$
> 3S = \frac{(2n + 3)n(n+1)}{2} - \frac{n(n+1)}{2}
> $$
> 
> $$
> S = \frac{(2n + 1)n(n+1)}{6}
> $$

$$
\sum_{1 \le i \le n}{\frac{1}{i}} \approx \ln{n}
$$

> This is because
> $$
> \int_{1}^{n}{\frac{1}{x}dx} = \ln{n}
> $$
> 
> we have the approximation above by imagining width 1 strips
> between each $x$ and $x+1$ of height $\frac{1}{x}$.
> This will resemble the area under curve roughly.

**In general**, we can use approximation:

$$
\sum\limits_{1 \le i \le n}{f(i)} \approx \int_{0}^{n}{f(x) dx}
$$

More examples:
- $\sum\limits_{1 \le i \le n}{\sqrt{i}} \approx \frac{2}{3} n^{\frac{3}{2}}$
- $\sum\limits_{1 \le i \le n}{\sqrt{\frac{n}{i}}} \approx 2 n$


### Progressions

For $x \neq 1$, it holds that:

$$
\sum_{0 \le i \le n}{x^i} = \frac{x^{n+1} - 1}{x - 1}
$$

> The derivation is quite simple.
> Let the sum be $S$, then:
> $$
> S = x^0 + x^1 + \ldots + x^n
> $$
> 
> $$
> \Rightarrow xS = x^1 + x^2 + \ldots + x^{n+1}
> $$
> 
> $$
> \Rightarrow xS - S = x^{n+1} - x^0
> $$
> 
> $$
> \Rightarrow S = \frac{x^{n+1} - 1}{x - 1}
> $$

This also leads to:
$$
x^n - 1 = (x - 1)(1 + x + x^2 + \ldots + x^{n-1})
$$

If $\vert x \vert \lt 1$, then for $n \rightarrow \infty$, we have:

$$
\sum_{0 \le i \le \infty}{x^i} = \frac{1}{1 - x}
$$

The most common example for this is:
$$
1 + \frac{1}{2} + \frac{1}{4} + \frac{1}{8} + \ldots = 2
$$

### Quadratic equations and inequalities

A quadratic equation has form $ax^2 + bx + c$ with $a \neq 0$.

If $x_0$ is a root of quadratic equation $ax^2 + bx + c$,
then $a{x_0}^2 + b{x_0} + c = 0$.

A quadratic equation can have upto 2 roots.

At least one root exists if $b^2 - 4ac \ge 0$. This term is commonly
referred as $D$.

If $D \ge 0$, then roots are given by:

$$
\frac{-b \pm \sqrt{D}}{2a}
$$

If $D = 0$ then only a single root $\frac{-b}{2a}$ will exist.


### Binomial theorem

$$
(a + b)^n = \sum_{0 \le i \le n}{\binom{n}{i} a^i b^{n-i}}
$$


### Series multiplication

For some $x_1,\ldots,x_n$ and $y_1,\ldots,y_n$

$$
(x_1 + \ldots + x_n)(y_1 + \ldots + y_n) = \sum_{1 \le i, j \le n}{x_i y_j}
$$

### Median properties

- If $x$ is median of $n = 2k + 1$ elements
  then $x$ is smallest element greater than or equal to $k + 1$ elements.

### Floor

Basics:
- $\lfloor a + x \rfloor = \lfloor a \rfloor + x$ if $x \in \mathbb{Z}$ 

Repeated floor division property:

$$
\left\lfloor \frac{\left\lfloor \frac{x}{u} \right\rfloor}{v} \right\rfloor
= \left\lfloor \frac{x}{uv} \right\rfloor
$$

### Pigeon Hole principle

If there are $N$ holes and some configuration of $N+1$ 
pigeons in them, then there always exists at least one hole
which has greater than or equal to 2 pigeons.

***