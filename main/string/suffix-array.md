---
layout: page
title: Suffix array
parent: Strings
math: true
nav_order: 3
---

# Suffix array

For a string $t$ of length $n$, suffix array
is an integer array of length $n$ which
denotes start indices of suffixes of $t$
if they were sorted (in lexicographical order).

Let $S$ be the suffix array, then it holds
that $t_{S_i, n} \lt t_{S_{i+1}, n}$ for all $i$ ($1 \le i \lt n$)

For example, let `t = "pabababq$"` (`$` is appended for convenience)

| Suffix index | Suffix |
| --- | --- |
| 0 | `pabababq$` |
| 1 | `abababq$` |
| 2 | `bababq$` |
| 3 | `ababq$` |
| 4 | `babq$` |
| 5 | `abq$` |
| 6 | `bq$` |
| 7 | `q$` |
| 8 | `$` |

After sorting:

| Suffix index | Suffix |
| --- | --- |
| 8 | `$` |
| 1 | `abababq$` |
| 3 | `ababq$` |
| 5 | `abq$` |
| 2 | `bababq$` |
| 4 | `babq$` |
| 6 | `bq$` |
| 0 | `pabababq$` |
| 7 | `q$` |

The suffix array is $[8, 1, 3, 5, 2, 4, 6, 0, 7]$

A suffix array is not very useful by itself but it is a pre-requisite
for a number of efficient string algorithms.

## Trivial computation

Simply sort by comparison.

Sample implementation:
```cpp
vector<int> suffix_array (string s) {
  vector<int> r (s.size());
  for (int i = 0; i < (int)r.size(); i++) r[i] = i;
  sort(r.begin(), r.end(), [&] (int i, int j) {
      return s.substr(i, s.size()) < s.substr(j, s.size());
  });
  return r;
}
```

## Faster computation - Prefix doubling method


### Rank system

This method relies on ranking prefixes of length $2^i$,
for $i = 0$ to $k$ where $k = \lceil \lg{n} \rceil$,
of all the suffixes of a string $s$.

The ranking system works as follows:
- $R_{i, j}$ is the rank of suffix $i$ based on prefix length $2^j$.
- $R_{i, k}$ is the rank of whole suffix $i$.
- Ranks may be any comparable types. Integer is a preferred choice for simplicity.
- For some $i_1, i_2$, comparison of $R_{i_1, j}$ and $R_{i_2, j}$
  equivalent to comparison of $s_{i_1, j_1}$ and $s_{i_2, j_2}$
  where $j_1 = \min(n, i_1 + 2^j - 1)$ and $j_2 = \min(n, i_2 + 2^j - 1)$.
  In simple terms, we use ranks to order substrings lexicographically.
- Only same length comparison is valid. Comparison of $R_{i_1, j_1}$
  and $R_{i_2, j_2}$ where $j_1 \neq j_2$ has no meaning.
- Rank of an empty string should be the lowest. $0$ is a suitable choice.
  $R_{i, j} = 0$ if $i \gt n$.

### Rank computation

For prefixes of length $1$, we may assign ASCII values as ranks.

If for some $j$, $R_{i, j}$ has been computed for all $i$, we
can then compute $R_{i, j+1}$ in the following way:
- Prefix of length $2^{j+1}$ of suffix $i$ is formed of two
  parts whose ranks are $R_{i, j}$ and $R_{i + 2^j, j}$.
  We have already computed those ranks.
- Ordering of rank pairs $(R_{i, j}, R_{i + 2^j, j})$
  is same as ordering of $R_{i, j+1}$. Hence we can sort the
  rank pair list and assign the smallest pair as rank $1$.
- For rest of the pairs in the sorted sequence, we can sequentially assign ranks.
  Let rank of previous pair $p$ be $r$. Let current pair be $p'$.
  - If $p' = p$, $r' = r$ (same rank)
  - Otherwise $p' \gt p$ thus $r' \gt r$. A simple assignment is $r' = r+1$

Finally, we can obtain the suffix array by sorting $(R_{i, k}, i)$
and taking the first part of the obtained sequence.

Sample implementation in C++:

```cpp
vector<int> calc(string s) {
  int n = s.size();
  int k = 0;
  while ((1 << k) < n) k++;
  vector<vector<int>> r(k + 1, vector<int>(n, 0));

  // ranks for length 1
  for (int i = 0; i < n; i++) r[0][i] = s[i];

  auto rank_pair = [&] (int i, int j) {
    int u = r[j - 1][i];
    int t = i + (1 << (j - 1));
    int v = (t >= n ? 0 : r[j - 1][t]);
    return make_pair(u, v);
  };

  for (int j = 1; j <= k; j++) {
    vector<int> v(n, 0);
    for (int i = 0; i < n; i++) v[i] = i;
    sort(v.begin(), v.end(), [&] (int u, int v) {
      return rank_pair(u, j) < rank_pair(v, j);
    });
    r[j][v[0]] = 1;
    for (int i = 1; i < n; i++) {
      r[j][v[i]] = r[j][v[i - 1]] + (rank_pair(v[i - 1], j) < rank_pair(v[i], j));
    }
  }
  vector<int> a(n, 0);
  for (int i = 0; i < n; i++) a[i] = i;
  sort(a.begin(), a.end(), [&] (int u, int v) {
      return r[k][u] < r[k][v];
  });
  return a;
}
```

### Time complexity analysis

In each of the $k$ iterations, we sort the rank pairs.

Hence time complexity is $O(k n\lg{n}) = O(n(\lg{n})^2)$


### Further optimization

Notice that the sorting involves values having low magnitudes
(not greater than $n$, given $n$ is sufficiently large).

We can use a linear time sorting algorithm to reduce the running time.

Since we are sorting a list of pairs, we can sort in two passes:
- First sort by second element (which has lesser significance)
- *Stable* sort by first element (because we need to retain the relative order of second elements)

We can use a stable sort linear-time algorithm like *counting sort*.

Sample implementation in C++:

```cpp
vector<int> suffix_array (string s) {
  int n = s.size();
  int k = 0;
  while ((1 << k) < n) k++;
  vector<vector<int>> r(k + 1, vector<int>(n, 0));

  // ranks for length 1
  for (int i = 0; i < n; i++) r[0][i] = s[i];

  auto rank_pair = [&] (int i, int j) {
    int u = r[j - 1][i];
    int t = i + (1 << (j - 1));
    int v = (t >= n ? 0 : r[j - 1][t]);
    return make_pair(u, v);
  };

  for (int j = 1; j <= k; j++) {
    int m = max(n + 1, 256); // considering ascii ranks
    // pass 1
    vector<int> cnt(m, 0);
    for (int i = 0; i < n; i++) {
      auto [u, v] = rank_pair(i, j);
      cnt[v]++;
    }
    for (int i = 1; i < m; i++) cnt[i] += cnt[i - 1];

    vector<int> a(n);
    for (int i = n - 1; i >= 0; i--) {
      auto [u, v] = rank_pair(i, j);
      int x = --cnt[v];
      a[x] = i;
    }
    // a: sorted by 2nd element

    // pass 2
    cnt.assign(m, 0);
    for (int i = 0; i < n; i++) {
      auto [u, v] = rank_pair(i, j);
      cnt[u]++;
    }
    for (int i = 1; i < m; i++) cnt[i] += cnt[i - 1];

    vector<int> b(n);
    for (int i = n - 1; i >= 0; i--) {
      auto [u, v] = rank_pair(a[i], j);
      int x = --cnt[u];
      b[x] = a[i];
    }

    r[j][b[0]] = 1;
    for (int i = 1; i < n; i++) {
      r[j][b[i]] = r[j][b[i - 1]] + (rank_pair(b[i - 1], j) < rank_pair(b[i], j));
    }
  }
  vector<int> a(n, 0);
  for (int i = 0; i < n; i++) a[i] = i;
  sort(a.begin(), a.end(), [&] (int u, int v) {
      return r[k][u] < r[k][v];
  });
  return a;
}
```

Time complexity after this optimization is $O(n \lg{n})$.

A more compact and efficient implementation (which is a bit tricky to understand at first):
```cpp
vector<int> suffix_array (const string& s) {
  int n = s.size();

  vector<int> r(n); // rank, 1D for less space usage
  for (int i = 0; i < n; i++) r[i] = s[i];

  // gives a part of rank pair
  // full rank pair for suffix i, prefix len 2k is given by (rp(i, 0), rp(i, k))
  auto rp = [&] (int i, int k) {
    int x = i + k;
    return x >= n ? 0 : r[x];
  };

  // for counting sort
  int m = max(n + 1, 256);
  vector<int> cnt(m);

  vector<int> b(n); // tmp array for cnt sort
  vector<int> tmp(n); // tmp array for new ranks

  // counting sort: z decides whether first or second element of rank pair
  auto cnt_sort = [&] (vector<int>& v, int z) {
    for (auto& x : cnt) x = 0;
    for (int i = 0; i < n; i++) cnt[rp(v[i], z)]++;
    for (int i = 1; i < m; i++) cnt[i] += cnt[i - 1];

    for (int i = n - 1; i >= 0; i--) b[--cnt[rp(v[i], z)]] = v[i];
    swap(v, b);
  };

  // result: we pass it to counting sort every time
  vector<int> a(n);
  for (int i = 0; i < n; i++) a[i] = i;

  for (int k = 1; k < n; k <<= 1) {
    cnt_sort(a, k);
    cnt_sort(a, 0);
    int rank = 1;
    tmp[a[0]] = rank;
    for (int i = 1; i < n; i++) {
      if (rp(a[i - 1], 0) != rp(a[i], 0) || rp(a[i - 1], k) != rp(a[i], k))
        rank++;
      tmp[a[i]] = rank;
    }
    if (rank == n) break; // all ranks are different, can break out
    swap(r, tmp);
  }
  return a;
}
```

## Longest Common Prefix (LCP) array

LCP is yet another pre-requisite for many algorithms related to strings.

### Definition

For a string $t$ of length $n$, LCP array $L$ can be defined for $1 \le i \lt n$ as:

$L_i$ is the length of longest common prefix of suffix $i$ and the preceding
suffix (in lexicographically sorted order).

We define $\Phi_i$ as index of suffix preceding suffix $i$ in sorted order.
In terms of suffix array, this simply means $\Phi_{S_i} = S_{i-1}$ (for $i \gt 0$).

> We append a sentinel symbol to the string (usually `$`) which is lesser in value than other characters (for convenience).
> It is always the first suffix in sorted order and thus has no preceding suffix, i.e.,
> $\Phi_n = 0$ (or $\Phi_{n-1} = -1$ if $0$-indexed)

We can now define $L_i$ as length of LCP of suffix $i$ and $\Phi_i$.

### Computation: Trivial

Assuming that $\Phi$ is available (which requires a suffix array),
we can compute $L$ trivially by iterating through suffix $i$ and suffix $\Phi_i$,
for each $i$.

This will take $O(n^2)$ time for a string of length $n$ which is not acceptable for decently sized strings.

Sample code in C++:
```cpp
vector<int> lcp_trivial(string t, vector<int>& sa) {
  int n = t.size();
  vector<int> p(n, -1), l(n, 0);
  for (int i = 1; i < n; i++) p[sa[i]] = sa[i - 1];
  for (int i = 0; i < n; i++) {
    if (p[i] == -1) continue;
    /* index bound check not required because inequality condition will occur first */
    while (t[i + l[i]] == t[p[i] + l[i]]) l[i]++;
  }
  return l;
}
```

### Computation: Binary lifting on rank matrix

This approach exploits the already computed rank matrix.
Note that:
- $R_{i, j}$ is the rank of suffix $i$ based on prefix length $2^j$
- We can basically check equality of any length $2^j$ substrings in $O(1)$:
  If $R_{i_1, j} = R_{i_2, j}$, then substrings of $s$ having length $2^j$ starting at $i_1$
  and $i_2$ are equal.

We can compute LCP array in $O(n\lg{n})$.

### Computation: Permuted LCP theorem 

Base step: $L_1$ can be computed in $O(m)$ where $m$ is the length of LCP of suffix $1$
and $\Phi_{1}$.

To compute $L_{i+1}$ if $L_i$ is already computed, we can reuse some information.
Let $L_i = m$, then $L_{i+1}$ will be at least $m-1$ because:
- In general, suffix $i+1$ is just suffix $i$ without the first character
- Since suffix $i$ and $\Phi_{i}$ have common prefix of length $m$,
  then suffix $\Phi_i + 1$ and $i+1$ will have common prefix of length $m-1$.
  Since suffix $\Phi_i$ comes before suffix $i$ in sorted order,
  same holds for suffix $\Phi_i + 1$ and suffix $i + 1$.
- Either suffix $\Phi_i + 1$ immediately precedes suffix $i + 1$ in sorted order,
  i.e., $\Phi_{i+1} = \Phi_i + 1$ or there are a number of suffixes in between,
  all of which will have first $m-1$ characters same (can be proved by contradiction).
- Thus, we can start comparison of suffix $i+1$ and $\Phi_{i+1}$ skipping
  first $m-1$ characters. Then, trivial way of computation can be followed.

Now we state the theorem which helps in determining time complexity of the algorithm:

**Permuted LCP theorem**: *Sum of (absolute) differences between $L_i$ and $L_{i+1}$, over all $i$,
is $O(n)$*

With this theorem, computation of $L$ also becomes $O(n)$ using the
above technique of skipping already computed prefix.

Sample implementation in C++:

```cpp
vector<int> lcp (const string& t, const vector<int>& sa) {
  int n = t.size();
  vector<int> p(n, -1), l(n, 0);
  for (int i = 1; i < n; i++) p[sa[i]] = sa[i - 1];
  for (int i = 0; i < n; i++) {
    if (p[i] == -1) continue;
    if (i > 0) l[i] = max(0, l[i - 1] - 1);
    while (t[i + l[i]] == t[p[i] + l[i]]) l[i]++;
  }
  return l;
}
```

Note that the only difference between trivial and efficient implementation
above is line (which is the *skipping* part):
```cpp
if (i > 0) l[i] = max(0, l[i - 1] - 1);
```

### Permuted LCP array

A slightly convenient definition of LCP array for some cases is array $P$
where $P_i$ is length of LCP of $S_i$ and $S_{i-1}$.

> Comparison of two definitions:
> - $L_i$ is length of LCP of $i$ and $\Phi_i$
> - $P_i$ is length of LCP of $S_i$ and $S_{i-1}$

$P$ and $L$ are inter-convertible: $P_i = L_{S_i}$

Sample implementation is similar to LCP:
```cpp
vector<int> plcp (const string& t, const vector<int>& sa) {
  int n = t.size();
  vector<int> p(n, -1), l(n, 0);
  for (int i = 1; i < n; i++) p[sa[i]] = sa[i - 1];
  for (int i = 0; i < n; i++) {
    if (p[i] == -1) continue;
    if (i > 0) l[i] = max(0, l[i - 1] - 1);
    while (t[i + l[i]] == t[p[i] + l[i]]) l[i]++;
  }
  vector<int> r(n);
  for (int i = 0; i < n; i++) r[i] = l[sa[i]];
  return r;
}
```

***