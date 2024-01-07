---
layout: page
title: Suffix array - applications
parent: Strings
math: true
nav_order: 4
---

In following discussions we will assume that we can compute
suffix array for a string of length $n$ in $O(n\lg{n})$ and
LCP array for the same in $O(n)$.

## String matching

We are given text $t$ and a pattern $p$.
Find the index of first occurence of $p$ in $t$ (if exists).


### Extension

For a given text $t$, you will be given $q$ queries with each giving
you a pattern to search for.

## Longest repeating substring

Given a string $t$, find the longest substring which occurs at least two times in $t$ (at different positions, of course).

## Longest common substring

Given two string $s$ and $t$, find the length of longest common substring
among them.

### Extension

Find the same for more than two strings.

#### LCS of $k$ out of $n$ strings.

The approach can be briefly described as:
- Concatenate the strings after adding a unique marker to end of each.
- Construct suffix array and LCP array.
- Construct group or 'id' array which identifies the
  string from suffix index in $O(1)$
- Construct static RMQ (sparse table) for LCP array.
- For some index $i$ in suffix array, find minimum $j$ such that $i \le j \le n$
  and suffixes denoted by $S_i, \ldots, S_j$ contain $k$ distinct strings
  (as per the groups or ids computed earlier). Range minimum in LCP array
  for range $(i, j)$ gives the longest common prefix for $k$ out of $n$
  strings, if we start at $i$
- To do this for all $i$ ($1 \le i \le n$), we can use a two-pointers approach.


> **Adding large number of sentinel characters / markers**
> 
> We need unique markers to separate strings in order to find LCP correctly.
> If duplicate markers are used, we may report a LCP containing that marker.
> 
> To add an arbitrary number of distinct markers, we introduce an offset to ASCII characters.
> 
> For example, integer value $65$ represents `A`, but we can instead assume that $165$ represents `A`
> thus gaining at least $100$ reserved values to be used as markers.


> **Identifying groups or ids**
> 
> Suppose the overall string is made from concatenation of $s_1, \ldots, s_n$.
> Given the suffix $x$, we can identify the string which occurs at its beginning
> through a binary search on prefix sum array of lengths of $s_i$.
> 
> These can also be pre-computed using $O(n)$ extra space allowing $O(1)$ query.


## Distinct substrings of a string

Given a string $t$ of length $n$, we are asked to find the
number of distinct substrings of $t$.

One way to traverse through *all* substrings is by going through the prefixes
of all suffixes.

Traversing the suffixes in the sorted order, i.e, using the suffix array,
has a particular advantage: *if LCP of current and previous suffix is $k$,
then first $k$ prefixes would be duplicates*.

The prefixes after $k$ are guaranteed to be the *first* instances.
It can be shown easily that none of them will occur before the current suffix.
They may occur afterwards but since we never count any duplicate prefix, we will count all distinct substrings.

If $S$ is the suffix array and $P$ is the (permuted) LCP array, then number
of distinct substrings is given simply by:

$$
\sum\limits_{1 \le i \le n}(n - S_i + 1) - P_i
$$

$(n - S_i + 1)$ is the length of suffix $S_i$ and it is obvious that
$P_i \le (n - S_i + 1)$ in all cases.

We can also find positions of such substrings through this way.

> Related problems:
>  - [CF 271D](https://codeforces.com/contest/271/problem/D)
>  - [ADASTRNG](https://www.spoj.com/problems/ADASTRNG/)
***