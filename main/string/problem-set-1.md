---
layout: page
title: Problem set - 1
parent: Strings
math: true
nav_order: 7
---

#### Problem

Given a palindrome, find out if there is a way to divide it
into non-zero substrings $A,B$ such that $BA$ is a palindrome
*not equal* to original palindrome.

#### Solution

TODO

> Used in CF-1113D to find case for answer = 1

#### Problem

Given a list $w$ of $n$ unique strings.

There will be $q$ queries: given a string $s$ report
whether there exists a pair $(i, j)$ such that
$s = w_i w_j$ (concatenation). 

Constraints:
- $n \le 1000$
- Length $m$ of any string is $1 \le m \le 1000$
- All strings are formed of lowercase letters
- $q \le 1000$

#### Solution

If we consider all pairs, by concatenating them and comparing,
the running time will be $O(n^2 m)$ for each query.

If there was a single query, we could have worked with finding
prefixes and suffixes and matching their lengths.

In these cases the only viable solution becomes hash.
We need a hashing function that allows combining hashes
producing result equivalent to concatenating and then hashing.

A simple hashing scheme like treating the string as a base-26
number (modulo some large number) works here. For combining hashes,
we will also need the length of initial string.

Let $x, y$ be hashes of strings $u, v$. If length of $v$
is $k$, then combined hash will be given by:
$(x \cdot 26^k + y) \bmod M$.

Overall this can be solved by:
- First calculate individual hashes in $O(nm)$
- Then calculate hashes of all possible concatenated pairs in $O(n^2)$
- For each query, calculate hash and check if that exists in the above computed set

#### Problem

Given list $s$ of $n$ unique strings, find pairs which
form a palindrome on concatenation.

Constraints:
- $n \le 1000$
- $\vert s_i \vert \le 100$
- Each string consists of lowercase letters

#### Solution

In this problem we need a way to check **fast** whether
a pair of strings forms a palindrome.

String hashing (which also supports extending hash, imitating concatenation)
can allow this.

We can compute hashes of each string and its reversed version.

Then, for each pair of strings $a, b$ we have to check whether
the following holds:

$$
f(h(a), h(b)) = f(h(b'), h(a'))
$$

Here $h$ is the hash function and $f$ is the combine function.

Note that the combine function also requires length of strings being combined
(or at least the second string).

Time complexity is $O(n^2 + \sum{l_i})$ where $l_i$ is length of word $i$.

There is another approach in which we get a quadratic running
time in terms of word lengths.

#### Approach 2

> The first approach is much simpler and preferred.
> This approach is beneficial when number of words is much
> higher than maximum length. Even then, the complete
> analysis is difficult because the solution can have $O(n^2)$ pairs.

If string $a$ and $b$ concatenate to form a palindrome and $a$ is not the longer string,
then $b = pa'$ where $a'$ is $a$ reversed and $p$ is a palindrome or an empty string.
In this case $ab$ is a palindrome. Similarly, if $b = a'p$ then $ba$ is a palindrome.

We will look at each string as $b$, longer or equal length string in the
concatenation. Then, we will find all strings $a$ such that
$b = a'p$ or $b = pa'$.

We may hash the strings and store the mapping of each hash to index.
It is possible because all strings are unique.

Running time $O(\sum{ {l_i}^2 })$
or in simpler terms $O(nk^2)$ where $k$ is maximum of all string lengths.

***