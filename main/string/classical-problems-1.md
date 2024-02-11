---
layout: page
title: Classical problems - 1
parent: Strings
math: true
nav_order: 1
---

## String matching

Given text $t$ (length $n$), find occurence(s) of string $s$ (length $m$) in it.

There are many ways to solve this problem. We provide comparison of some:

- Trivial: $O(nm)$
- Rabin-Karp algorithm (probabilistic matching): $O(n)$
- KMP and Z-algorithm: $O(n + m)$
  - Good for cases when there is a single pattern string
- Suffix trie: $O(m)$ but building the tree takes $O(n^2)$ time
- Suffix tree : $O(m + k)$
  where $k$ is the number of occurences of pattern.
- Suffix Array : $O(m \lg{n})$ with $O(n \lg{n})$ build time.
  - It is also possible to build suffix array in linear time.

### Rabin-Karp algorithm

This algorithm is based on calculating hash of all substrings
of $t$ of length $m$ and reporting a match if the hash matches that
of $s$.

To calculate hashes efficiently, it uses a *rolling* hash function.

> A rolling hash is a hash function where the input is hashed in a window that moves through the input.
> A few hash functions allow a rolling hash to be computed very quickly—the new hash value is rapidly calculated given only the old hash value, the old value removed from the window, and the new value added to the window

A simple rolling hash function is:
$$
f(s) = \left( \sum_{1 \le i \le n}{B^{n-i} s_i} \right) \bmod P
$$

Here $s$ is a string of length $n$ and $s_i$ denotes the numerical value
of character at index $i$. In practice, for a lowercase string, character $\text{a}$ is assigned value $0$
and $\text{z}$ is assigned $25$. The base $B = 26$ in such cases.
The modulus part controls the domain of output. Note that
lesser value of $P$ may result into higher number of collisions.

Now the important part is *rolling*, i.e. removing first value in window from hash
and adding a new value to window:

- Removing a value: let $s'$ be $s_{2, \ldots, n}$

  $$
  f(s') = (f(s) - B^{n-1} s_1) \bmod P
  $$

- Adding a value: let $s'$ be $s$ with one more character $s_{n+1}$

  $$
  f(s') = (Bf(s) + s_{n+1}) \bmod P
  $$

One may choose to verify, after hash value match, that the occurence is actually present.
In worst case this may result into $O(nm)$ time complexity.

### Knuth-Morris-Pratt algorithm

KMP algorithm is based on prefix function which we will describe below.


Note: For a string $s$, let $s_{i,j}$ denote
substring starting at index $i$ and ending at $j$
(assume $1$ indexing).

#### Prefix function definition

For a string $s$ of length $n$, compute
an array $P$ of length $n$ such that $P_i$ gives
length of longest suffix of $s_{1,i}$ which
is also a prefix of $s$ (having length lesser than $i$)

> This is also called Knuth-Morris-Pratt prefix function
> or $\pi$ function

#### Ideas

Suppose we have already computed $P_1,\ldots,P_{i-1}$
and we want to compute $P_i$.

We have $P_i \le P_{i-1} + 1$. This is because, assume,
$P_i \gt P_{i-1} + 1$ and $P_i = k$ then we get
prefix of length $k-1$ as we move one index back to $i-1$. But $P_{i-1} \lt k - 1$ from
above assumption. Thus, the assumption is wrong.

The best we can get is $P_i = P_{i-1} + 1$
which requires $s_i = s_{P_{i-1} + 1}$.

What is the next maximum length we can get?

Suppose it is $P_i = l$ where $l \lt P_{i-1} + 1$. Then:
- Suffix of $s_{1,i}$ of length $l$ is also a prefix of $s$.
- Also, directly following from above, suffix of $s_{1,i-1}$ of length $l-1$
is also a prefix.
- But we also know that suffix of $s_{1,i-1}$ of length $P_{i-1}$ (note that $P_{i-1} \gt l-1$)
  is also a prefix.
- We have smaller instance of a problem, let $k = P_{i-1}$,
  then we need to find the largest suffix of $s_{1,k}$ which is also a prefix of $s$.
  We use already computed results: $P_{k} = l - 1 \Rightarrow l = P_k + 1$

We can repeat this process until we get to length $0$.

#### Algorithm

```
# assume string s of length N given
P = list of length N
P[1] = 0
for i = 2 to N
    j = P[i - 1]
    while j > 0 and s[j + 1] != s[i]
        j = P[j]
    if s[j + 1] = s[i]
        P[i] = j + 1
    else
        P[i] = 0
```

Note that $P_i \lt i$ $\forall$ $1 \le i \le n$ hence
the `while` loop making use of `j = P[j]` is guaranteed
to end.

To find time complexity, notice that:
- Due to `while` loop, `j` may decrease down to $0$ (and at least by $1$ per iteration).
- `P[i] = j + 1` and `j = P[i - 1]` means for the next iteration, value of `j` is
  increased by 1 (case where `P[i] = 0` means no increase)
- Since we can 'generate' value at most $n$ for `j` in all
iterations combined, we can decrease it at most the same amount (i.e., $n$ times)
without going negative.

Hence, time complexity is $O(n)$.

#### Implementation

```cpp
vector<int> prfunc (string s) {
  int n = s.size();
  vector<int> p(n, 0);
  for (int i = 1; i < n; i++) {
    int j = p[i - 1];
    while (j > 0 && s[j] != s[i]) j = p[j - 1];
    p[i] = (s[j] == s[i] ? 1 + j : 0);
  }
  return p;
}
```

Note:
- Due to $0$-indexing, `s[j] != s[i]` is used instead of `s[j + 1] != s[i]`
- For the same reason, `j = p[j - 1]` instead of `j = p[j]`

#### String matching using prefix function

Let the given text be string $t$ and pattern to match be $s$.

Then we can first compute prefix function $P$ for $s$.

We can then use a similar idea as prefix function.
For $t_{1, i}$ let largest suffix which is also a prefix
of $s$ is $k$. Then if $k = n$, we have a match for $s$ as $t_{i-n+1,i}$.

Let's look at a very similar algorithm
```
# s (length N), t (length M)
# assume P computed for s
  
1   j = 0
2   for i = 1 to M
3       while j > 0 and t[i] != s[j + 1]: j = p[j]
4       if t[i] != s[j + 1]: continue
5       j = j + 1
6       if j == N
7           output match starting at i - n + 1
8           j = p[j]
```

> Remember $j$ as length of longest suffix of $t_{1, i-1}$
> which is prefix of $s$.

Here $j$, at start iteration $i$, denotes the
length of longest prefix of $s$ (but not whole $s$) which also a suffix of $t_{1,i-1}$.
(or $0$ if $i = 1$).

If $t_i = s_{j+1}$, we extend that prefix to length $j+1$ otherwise
we find the next longest prefix satisfying the condition (making use of prefix function).

In line 4, `t[i] != s[j + 1]` there is no match possible, $j = 0$ in that
case and we move directly to next iteration.

In line 5, we do the extension to length $j+1$. Now, if
this length becomes $n$, then we output a match.
The last line finds the next longest prefix (because we can't take whole $s$).

For time complexity, we can apply the same argument (based on $j$)
as we did for prefix function. Time complexity is $O(m + n)$.

Implementation:
```cpp
vector<int> matches (string t, string s) {
  int m = t.size(), n = s.size();
  vector<int> p = prfunc(s);
  vector<int> ans;
  for (int i = 0, j = 0; i < m; i++) {
    while (j > 0 && t[i] != s[j]) j = p[j - 1];
    if (t[i] != s[j]) continue;
    j++;
    if (j == n) {
      ans.push_back(i - n + 1);
      j = p[j - 1];
    }
  }
  return ans;
}
```

### Z-algorithm

Z-algorithm is based on Z-function which is similar to prefix function used in KMP.

For a string $s_{1,n}$, we wish
to compute array $z$ such that $z_i$
gives max $k$ such that $s_{1,k} = s_{i,i+k-1}$.

In other words, $z_i$ gives length of longest
prefix of $s$ that is also a prefix of $s_{i,n}$.

#### Computing Z-function trivially

```
s = "..."
z = [0, ...]

for i = 1 to n
    let x = 0
    while i + x <= n and s[1 + x] == s[i + x]
        x = x + 1
    z[i] = x
```

#### Matches

For each $i$ such that $z_i \gt 0$,
we have a string $s_{i, \ldots, i + z_i - 1}$
as a prefix of $s$.

Let $l = i$ and $r = i + z_i - 1$ then
$s_{l, \ldots, r}$ is a prefix of $s$.

We will get a $(l, r)$ pair for each $i$ where 
$z_i \gt 0$.

#### Using earlier matches

Suppose we are at index $i$ and we want to compute
$z_i$.

Suppose there is some $(l, r)$ from earlier matches ($1, \ldots, i-1$)
such that $r \ge i$. Then, visualize this:

```
abcdefgh_____________
____________abcdefgh
            l i    r
```

(letters only for representation, not reflecting actual values)

In this example, we know that:
- `s[l...r] = s[1...8] = abcdefgh`
- `s[i...r] = s[3...8] = cdefgh` (simple implication)

Suppose `z[3] = 5`, then we know that:
- `cdefg` is a prefix of `s`
- `cdefgh` is **not** a prefix of `s`

For `i`, can `z[i] > 5` ? Let's check whether `z[i] = 6`
- This would mean that `cdefgh` is a prefix of `s`
- But `s[3...8] = s[i...r] = cdefgh` and `z[3] = 5`
- Thus, `z[i] = 5`

Now, suppose `z[3] = 6`, then:
- `cdefgh` is a prefix of `s`
- `z[i] = 6` at least because we don't have
  knowledge of further letters.
- In this case we can explore trivially to check whether
  `z[i] > 6`.

In the algorithm, we will just re-use the information as presented.

#### Algorithm

- Assume $0$-indexing and $z$ of length $n$ being initialized with $0$
  and string $s$ of length $n$.

```
L, R = 0, 0
for i = 0 to n - 1:
    if i > R do:
        # Trivial calculation
        while (i + z[i] < n) and s[z[i]] == s[i + z[i]]
            z[i] += 1
        L, R = i, i + z[i] - 1
    else:
        k, len = i - L, R - i + 1
        if z[k] < len:
            z[i] = z[k]
        else:
            z[i] = len
            # Continue trivial calculation
            while (i + z[i] < n) and s[z[i]] == s[i + z[i]]
                z[i] += 1
            L, R = i, i + z[i] - 1
```

Time complexity can be shown as $O(n)$ on basis of `R` pointer.
Notice how in (and after) all the `while` loops, the number of iterations
equal increment of `R` (and `R` is never decremented and never exceeds $n$)

In implementation, we can do variable replacement to reduce code:
```py
def z_alg(s):
    n = len(s)
    z, l, r = [0] * n, 0, 0
    for i in range(n):
        if i > r:
            l = r = i
            while r < n and s[r - l] == s[r]: r += 1
            z[i], r = r - i, r - 1
        elif z[i - l] < r - i + 1:
            z[i] = z[i - l]
        else:
            l = i
            while r < n and s[r - l] == s[r]: r += 1
            z[i], r = r - i, r - 1
    return z
```

Implementation in C++:
```cpp
vector<int> zcalc (string s) {
  int n = s.size(), l = 0, r = 0;
  vector<int> z(n, 0);
  auto calc = [&] (int l1, int r1, int i) {
      l = l1; r = r1;
      while (r < n && s[r - l] == s[r]) r++;
      z[i] = r - i; r--;
  };
  for (int i = 0; i < n; i++) {
    if (i > r) calc(i, i, i);
    else if (z[i - l] < r - i + 1) z[i] = z[i - l];
    else calc(i, r, i);
  }
  return z;
}
```

> Some example problems:
> - [CF-126B](https://codeforces.com/contest/126/problem/B)
> - [abc284_f](https://atcoder.jp/contests/abc284/tasks/abc284_f)

***