---
layout: page
title: Classical problems - 2
parent: Strings
math: true
nav_order: 2
---

## String alignment

### General variation
Given strings $a$ and $b$ of lengths $n$ and $m$, insert spaces (possibly in both, in multiple locations)
such that we get the maximum alignment score.

Score is computed as $c_1\alpha + c_2\beta + c_3\gamma$ where:
- $c_1$ is number of matches, and $\alpha$ is score for each
- $c_2$ is number of mismatches, and $\beta$ is score for each
- $c_3$ is number of positions where one of the strings has a blank, and $\gamma$ is score for each

> Note: In an optimal solution, there won't be any position where
> there are spaces in both strings.

#### Needleman-Wunsch's DP Solution

We can also interpret the given problem as: given strings $a$ and $b$,
obtain the best alignment by modifying $a$ using *insert* or *delete* operations.
The insert operation can be thought of as adding a gap in $a$ and 
the delete operation as adding a gap in $b$. It is illustrated in the example below:

```
Input:
a = "test"
b = "toast"

Operations on 'a' to make it "toast" (we can make it as any string)
- delete e (at position 2)
- insert o (at position 3)
- insert a (at position 4)

Correlation with gaps:
a = "te__st"
b = "t_oast"
```

Let $f_{i, j}$ be the best alignment score of $a_{1,i}$ and $b_{1, j}$

> Note: $a_{i, j}$ is a shorthand notation to denote substring of
> $a$ starting at index $i$ and ending at $j$.

Let $X$ be the score for insertion and $Y$ be the score for deletion.
To correlate with gaps, $X = Y = \gamma$.

Base Cases:
- $f_{0, 0} = 0$
- $f_{0, i} = i \cdot X$ ($i$ insertions for $b_{1, i}$)
- $f_{i, 0} = i \cdot Y$ ($i$ deletions for $a_{1, i}$)

Recurrence relation:

$ f_{i, j} = \max (u_1, u_2, u_3)$ where:

- $u_1 = f_{i-1,j-1} + h_{i, j}$
- $u_2 = f_{i, j-1} + X$
- $u_3 = f_{i-1, j} + Y$

$h_{i,j}$ is the character match (or mismatch) score: $\alpha$ if $a_i = b_j$
and $\beta$ otherwise.


To obtain the actual alignment, we backtrace the path from $f_{n, m}$ to $f_{0, 0}$

This runs in $O(nm)$ time and uses $O(nm)$ space.

> Edit distance is a classical problem of finding minimum number of insertions
> and deletions to convert one string to the other.
> 
> By substituting $X = -1, Y = -1, \alpha = 0, \beta = -\infty$ in the above
> algorithm, we get the edit distance.
> 
> We will get a negative score because we maximize. It has to be negated.


#### Local alignment

The above algorithm gives the optimal alignment considering whole of $a$ and $b$.
This is called *global alignment*. If we want to find the optimal alignment of *any substrings* of the input strings,
this is called *local alignment*.

In local alignment, we can never get a negative score because we can always
take empty substrings from both strings and get zero score.

The algorithm for solving this (attributed to Smith & Waterman) is
similar to Needleman-Wunsch algorithm.

Let $f_{i, j}$ be the maximum score on matching some substring of $a$ ending at $i$
with some substring of $b$ ending at $j$.

Base Cases:
- $f_{0, 0} = f_{0, i} = f_{i, 0} = 0$ (assuming normal scenario where $X, Y \lt 0$)

Recurrence relation:

$ f_{i, j} = \max (0, u_1, u_2, u_3)$ where:

- $u_1 = f_{i-1,j-1} + h_{i, j}$
- $u_2 = f_{i, j-1} + X$
- $u_3 = f_{i-1, j} + Y$

> Notice that we include $0$ in the maximum because of empty strings as the worst case

To find the best local alignment, we need to find maximum $f_{i, j}$ over all $i, j$.

To obtain the optimal alignment, backtracking can be used. Let $f_{u, v}$ be the 
maximum, then we start at $f_{u, v}$ and end at some $p, q$ such that $f_{p, q} = 0$.

***