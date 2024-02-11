---
layout: page
title: Adhoc tips - 2
parent: Programming
math: true
nav_order: 6
---

### Handling integer overflows in tricky problems

#### Illustration 1

Find greatest multiple of positive integer $x$ which
is not greater than $M$ (maximum 4-byte signed integer value)
only by addition.

Overflow and other means of storing are not allowed, how to do it?

Simple way but overflows:
```
ans = X
while (ans + X <= MAX) ans += X
```

Correct approach
```
ans = X
while (ans + X <= MAX) {
  ans += X
  if (ans > MAX - X) break;
}
```

Similarly, in case of multiplication
if `ans * x` (both positive) may overflow, first check `ans > INT_MAX / x`.
Is this correct?
- One may argue that `INT_MAX / x` is floored division.
  But since `ans` is an integer, even the floored result
  must be greater.
- Does this work for negative overflow (if `ans` is negative
  and `x` is positive):
  `ans > INT_MIN / x` works (at least in C++ where integer division
  truncates towards `0`).

### Grid traversals

#### Iterate through directions

It is useful to handle four directions (up, down, left, right)
in the following way:
```
vector<int> dr = {-1, 1, 0, 0};
vector<int> dc = {0, 0, -1, 1};

// move in direction 'dir' which is one of [0, 1, 2, 3]
void move (int& r, int& c, int dir) {
  r += dr[dir];
  c += dc[dir];
}
```

#### Spiral traversal of a 2D matrix

We are given a $n \times m$ matrix.
Starting from $(0, 0)$, return a sequence
of cells which represents spiral traversal (assume clockwise).

Implementation hints:
- using direction and bounds
- and decreasing bounds on traversal

```cpp
    pair<int, int> add (pair<int, int> a, pair<int, int> b) {
      return {a.first + b.first, a.second + b.second};
    }

    vector<int> spiralOrder(vector<vector<int>>& matrix) {
      vector<int> res;

      int n = (int)matrix.size(), m = (int)matrix[0].size();
      const int D = 4;
      vector<pair<int, int>> dir = { {0,1},{1,0},{0,-1},{-1,0} };
      vector<pair<int, int>> bounds = { {0,m-1},{n-1,m-1},{n-1,0},{0,0} };
      pair<int, int> pos = {0, 0};
      for (int i = 0, x = 0; i < n * m; i++) {
        if (pos == bounds[x]) {
          int nxt = (x + 1) % D;
          int prv = (x - 1 + D) % D;
          bounds[x] = add(bounds[x], dir[nxt]);
          bounds[prv] = add(bounds[prv], dir[nxt]);
          x = nxt;
        }
        res.push_back(matrix[pos.first][pos.second]);
        pos = add(pos, dir[x]);
      }
      return res;
    }
```

***