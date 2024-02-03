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

***