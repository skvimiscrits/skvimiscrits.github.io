---
layout: page
title: Bit manipulation - 3
parent: Programming
math: true
nav_order: 4
---

### General techniques

#### Starting from highest order bits

In problems related to bits where we need to maximize
the answer, we typically start with building
the answer from highest bits.
The algorithm (for maximizing the answer) can be summarized as follows:
- Set $i$ to highest bit.
- Set bit $i$ in answer.
- If no solution can be formed with this bit set, unset
  this bit.
- $i \leftarrow i - 1$ and repeat from step 2 unless $i = 0$

For minimizing, we try to find a solution in a similar way,
but trying to get highest bit as $0$.

### Illustrations

#### Illustration 1

*source: CF*

Given integers $a, b, r$ find the minimum of
$\vert (a \oplus x) - (b \oplus x) \vert$ among $0 \le x \le r$

Let's find the highest bit $i$ satisfying these conditions:
- $r \ge 2^i$
- $a_i \neq b_i$ ($a_i$ denotes bit $i$)

Bits higher than $i$ can not be changed in answer because they
satisfy one of above two:
- $r \lt 2^i$ : we can not set this bit.
- $a_i = b_i$ : choice of $x$ doesn't matter

Let $a'$ be $a$ considering only bits higher than $i$
and let $b'$ be defined in a similar manner.

Assuming $a \ge b$, we have two cases:
- $a' = b'$
  In this case we can set $x_i = 0$ and
  we can choose all smaller bits of $x$ freely in order
  to maximize $b$ (because $a_i = 1$). This is optimal
  because we can maximize $b$ in all bit positions which can
  be modified (i.e. where bit in $a$ and $b$ are different).
- $a' \gt b'$
  Here, we have to maximize $b$ starting from bit $i$ itself.
  We need to greedily set bits in $x$ in order to
  maximize $b$ as long as $x \le r$

These two cases are similar, and can be combined.
Sample code:
```cpp
Long solve (Long a, Long b, Long r) {
  if (a < b)
    swap(a, b);

  int k = 62;
  int diff = -1; // first different bit in a, b

  while (k >= 0) {
    Long t = (1LL << k);
    if (diff == -1 && ((a ^ b) & t))
      diff = k;
    if (((a ^ b) & t) && (r >= t))
      break;
    k--;
  }

  if (diff == k) // two cases described above
    k--;

  Long x = 0;
  for (int i = k; i >= 0; i--) {
    Long t = (1LL << i);
    if (((a ^ b) & t) && (b & t) == 0 && (x | t) <= r) {
      b ^= t;
      a ^= t;
      x |= t;
    }
  }
  return a - b;
}
```

#### Illustration 2

We are given an integer array $a$.
In one operation, we can replace any two adjacent elements
by their bitwise *and* (`&`)

We need to return the minimum value of bitwise *or* (`|`)
of all elements of $a$ after performing the above operation
at most $k$ times.

*Solution*

Can we check whether it is possible to get $x$ or lower
as result?

> Lower here does not mean less than in general sense.
> It means omitting some bits from $x$ (by the nature of operations).

The elements which have some bits set, which are not set 
in $x$, have to be combined with some other elements.

Formally, 
- Let's element $u$ satisfying `(x | u) = x` (a *good* element)
- Element which is not a good element is a bad element.
- A bad element has to be combined with some other element.
  - This consumes one operation
  - It is possible that multiple bad elements combine to form a good element.

Greedy strategy to eliminate bad elements using least operations:
- An isolated bad element (one with no neighboring bad element) will consume one operation
- Find first two consecutive bad elements $a_i, a_{i+1}$: combining these is never
  suboptimal.
  - Firstly either $i = 1$ or $a_{i-1}$ is a good element.
  - Suppose two were not combined in an optimal solution, then two operations
    are consumed. We can obtain an answer which is not worse by merging $a_i$ into $a_{i+1}$
    and then merging $a_{i+1}$ to its right (the left part ending at $a_{i-1}$ is unaffected).
  - Note that combining $a_i$ and $a_{i+1}$ may lead to a good element. 

If we can eliminate all bad elements by not using more than $k$ operations, a solution
if possible.

We will start with `x = 111..111` and start restricting the mask starting from higher order bits.
By restricting here, we mean changing `1` to `0` because that will require number of operations not lesser
than before it was removed.


Sample code:
```cpp
bool can (vector<int>& a, int k, int x) {
  int last = 0, good = 0, ops = 0;
  for (auto e : a) {
    if ((e | x) == x) {
      good++;
      if (last > 0)
        ops++; // combining last bad segment
      last = 0;
    } else if (last != 0) {
      last &= e;
      ops++;
      if ((last | x) == x) {
        last = 0;
        good++; // combined to form a good element
      }
    } else {
      last = e; // start a bad segment
    }
  }
  if (last > 0)
    ops++; // when a bad segment occurs at the end
  return (good > 0 && ops <= k);
}

int solve(vector<int>& a, int k) {
  const int K = 30;
  int ans = (1 << K) - 1;
  for (int i = K - 1; i >= 0; i--) {
    int t = (ans ^ (1 << i));
    if (can(a, k, t)) {
      ans = t;
    }
  }
  return ans;
}
```

#### Illustration 3


We are given integers $L, R, k$.
Select $k$ distinct integers between $L$ and $R$ (inclusive)
and do bitwise *and* of all of $k$ elements.

Maximize the value obtained through optimal selection of those
$k$ elements.

Constraints:
- $0 \le L \le R \le 10^{18}$
- $1 \le K \le (R - L + 1)$

*Solution*

Can we check if an integer $x$ can be a solution?
- Take all integers $y$ between $L$ and $R$ such that
  `(x & y) = x` (in other words, $y$ contains at least those
  bits which are present in $x$).
- If there are at least $k$ such integers, then $x$ can be
  a solution.

We will build the answer by starting from higher order bits.
It is obvious that for `x = 000..000` it is possible.
When we convert some `0` to `1` in `x`, we reduce (or precisely: not increase) the
set of integers which satisfy the above conditions.

Sample code to build the answer:
```cpp
Long solve (Long l, Long r, Long k) {
  Long ans = 0;
  for (int i = K; i >= 0; i--) {
    Long t = (1LL << i);
    if (calc(ans | t, l, r) >= k)
      ans |= t;
  }
  return ans;
}
```

*Subproblem 1*:
`calc(mask, l, r)` calculates the number of integers
between `l` and `r` which contain at least those bits which
are present in `mask`.

One way to do this is finding the start and end elements
in this range which are valid (elements `x` such that `(x & mask == mask)`).
We can then compute the difference by some way.

```cpp
Long calc (Long mask, Long l, Long r) {
  Long u = higher_mask(l, mask);
  Long v = lower_mask(r, mask);
  if (u > v)
    return 0;
  return mask_diff(u, v, mask);
}
```

*Subproblem 2*:
`higher_mask(x, mask)` computes the smallest integer `y`
such that `y >= x` and `(y & mask) == mask`.

Starting from higher order bits, when will we set a bit in `y`?
- When that bit is set in `x`.
- When `y >= x` can't be satisfied without this bit.

```cpp
Long higher_mask(Long x, Long mask) {
  Long res = 0;
  for (int i = K; i >= 0; i--) {
    Long t = (1LL << i);
    if ((mask & t) || (res | (t - 1)) < x) {
      res |= t;
    }
  }
  return res;
}
```

*Subproblem 3*:
`lower_mask(x, mask)` computes the maximum integer `y`
such that `y <= x` and `(y & mask) == mask`.

Similar to above, starting from higher order bits, we will set
a bit in `y` if:
- That bit is set in `x`
- When `y <= x` *can* be satisfied with this bit.

```cpp
Long lower_mask(Long x, Long mask) {
  if (mask > x)
    return 0;
  Long res = 0;
  for (int i = K; i >= 0; i--) {
    Long t = (1LL << i);
    if ((mask & t) || (res | mask | t) <= x) {
      res |= t;
    }
  }
  return res;
}
```

*Subproblem 4*:
`mask_diff(u, v, mask)` calculates the number of
integers between `u` and `v` if for both of these `mask` is a subset.
(in other words `(u & mask) == mask` and `(v & mask) == mask`)

Obviously, bits which are present in mask will be frozen. We
can extract out the integers from `u` and `v` without these bits
and return the difference.

```cpp
Long mask_diff (Long l, Long r, Long mask) {
  Long u = 0, v = 0;
  for (int i = K; i >= 0; i--) {
    Long t = (1LL << i);
    if ((mask & t) == 0) {
      u = (u << 1) + ((l & t) > 0);
      v = (v << 1) + ((r & t) > 0);
    }
  }
  return v - u + 1;
}
```

Let $n$ be the maximum number of bits (denoted by `K` in code).
`higher_mask`, `lower_mask` and `mask_diff` all run in $O(n)$.
This implies that `calc` runs in $O(n)$ which means that `solve` runs in $O(n^2)$.

*Advanced part: $O(n)$ solution*

First, we will discuss a more efficient implementation
of `higher_mask`, `lower_mask`.

In case of `higher_mask(x, mask)`, let `y` be the answer:
- `y` will have all bits of `x`.
- If `x <= mask` then `y = mask`, obviously. We will
  assume `x > mask` in further discussion.
- Consider the highest bit `i` which is set in `mask`
  but not in `x` (if there is no such bit then `x` is the answer):
  - We need to set bit `i` in order to satisfy `(y & mask) == mask`.
  - After setting this bit, it holds that `y > x` irrespective
    of values of lower order bits.
    - Why: Since `x > mask`, there will exist some bit `j > i` which
      is set in `x` and not set in `mask`.
  - Thus, we can set lower order bits same as those in mask.

Assuming the highest bit `i` which is set in `mask` but in `x` is
available to us as `p = (1 << i)` in $O(1)$,
we have $O(1)$ implementation of `higher_mask`:

```cpp 
Long higher_mask (Long x, Long mask, Long p) {
  if (x <= mask)
    return mask;
  if ((x & mask) == mask)
    return x;
  // Long p = msb((x & mask) ^ mask); <- provided
  Long t = (p << 1) - 1;
  Long res = (x & (~t)) | (mask & t);
  return res;
}
```

For `lower_mask(x, mask)`, let `y` be the answer:
- Firstly, if `x < mask`, there is no solution.
- Consider the highest bit `i` which is set in `mask`
  but not in `x` (if there is no such bit then `x` is the answer):
  - We need to set bit `i` in order to satisfy `(y & mask) == mask`.
  - After setting this bit, it holds that `y > x` irrespective
    of values of lower order bits. This violates requirement `y <= x`.
  - We will need to unset next higher bit in `x` which is not present in `mask`.
    - Since `x > mask`, there will exist some bit `j > i` which
      is set in `x` and not set in `mask`.
  - Let the next such higher bit be `j`, after we unset `j` in `x` we can set all smaller bits to `1`
    without violating any condition (and getting the maximum value)

Assuming the highest bit `i` which is set in `mask` but in `x` is
available to us as `p = (1 << i)` in $O(1)$,
we have $O(1)$ implementation of `lower_mask`:
```cpp
Long lower_mask(Long x, Long mask, Long p) {
  if (x < mask)
    return -1;
  if ((x & mask) == mask)
    return x;
  // Long p = msb((x & mask) ^ mask); <- provided

  // d = bits which are different in x and mask (greater than p)
  Long d = ((x ^ mask) & (~((p << 1) - 1))); 
  Long t = (d & (-d)); // bit at j as described above
  return (x ^ t) | (t - 1);
}
```

Now, `mask_diff(l, r, mask)` was computing the exact count of elements
between `l` and `r` being superset of `mask`. But the result of this
is only used for comparison with `k`.

If we directly take difference `r - l`, it will not make much sense.
- Let's define function `f(x, mask)` which removes all bits from `x`
  which are present in `mask` and re-arranges the remaining bits.
- In essense, `mask_diff(l, r, mask)` return `f(r, mask) - f(l, mask) + 1`.
- We claim that `f(r, mask) - f(l, mask) = f(r - l, mask)`. The reason why
  this holds is that the extra `1`s that appear as a result of subtraction
  in places where bits of mask are set are anyway removed.
  To illustrate:
  ```
  r    = 1111100
  mask = 0111100
  l    = 0111101

  f(r, mask) = 100
  f(l, mask) = 001
  difference = 011

  r - l = 0111111
  f(r - l) = 011
  ```
- In fact, `(r - l) & (~mask)` will give a number which will have
  some `0`s in places where `mask` bits were present.
- *Extrapolation*: If we extrapolate `k` to include these extra `0`s at
  position of `mask` bits, we can directly compare it with `(r - l) & (~mask)`.
  To illustrate the concept:
  ```
  r                  = 1110110
  mask               = 0110110
  l                  = 0110111
  (r - l) & (~mask)  = 0001001 (represents 3)

  k                  = 0000010 (7 in base 10)
  k'                 = 0001000 (extrapolated, represents 2)
  ```
- But this will compare `r - l` to `k`, we need to compare `r - l + 1 >= k`.
  For this, we can simply subtract `1` from `k`.

Extrapolation of `k` over mask can be done through
the following function. Given a bit `t`, it *inserts*
a `0` in that position in `k`
```cpp
Long explt (Long k, Long t) {
  Long res = ((k & (~(t - 1))) << 1);
  if (t > 0)
    res |= (k & (t - 1));
  return res;
}
```

The final solution in $O(n)$ is given below:
```cpp
Long solve (Long l, Long r, Long k) {
  k--;
  Long ans = 0;

  Long p = -1; // first bit in mask but not in l
  Long q = -1; // first bit in mask but not in r

  for (int i = K - 1; i >= 0; i--) {
    Long t = (1LL << i);

    Long k1 = explt(k, t); // temporary, extrapolated value

    Long p1 = p; // temporary value assuming bit 'i' will be set in the mask
    if (p1 == -1 && (l & t) == 0)
      p1 = t;

    Long q1 = q; // temporary value assuming bit 'i' will be set in the mask
    if (q1 == -1 && (r & t) == 0)
      q1 = t;

    Long mask = (ans | t);
    Long u = higher_mask(l, mask, p1);
    Long v = lower_mask(r, mask, q1);

    if (u <= v && ((v - u) & (~mask)) >= k1) {
      ans = mask;
      k = k1;
      p = p1;
      q = q1;
    }
  }
  return ans;
}
```

***