---
layout: page
title: Adhoc tips - 1
parent: Programming
math: true
nav_order: 5
---

### Language specific: C++

#### Optimizing code

- Create lesser objects, reuse them
  - Especially vectors
- Use iterators for maps when accessing same
  key multiple times instead of using `[]` accessor.

#### High precision floating point operations

For floating point operations, such as ceiling for 64-bit
integer range, i.e, $\approx 10^{18}$, it is advised not to use `double`
as it is accurate only up to 15-17 digits which may lead to
inaccurate calculations in this range. 

We must use `long double` instead, which supports higher
number of significant digits.

#### Sorting in using comparator function

If you don't correctly define the comparator function, bad things
may happen (like segmentation fault which is not obvious)

A comparator function `f(a, b)` returns `true` if `a < b`
, `false` otherwise

#### Min and max heaps

```cpp
template <class T> using max_heap = priority_queue<T>;
template <class T> using min_heap = priority_queue<T, vector<T>, greater<>>;
```

Common operations:
- `push(x)`
- `pop(x)`
- `top(x)`
- `size(x)`

#### Unique elements

```cpp
a.resize(distance(a.begin(), unique(a.begin(), a.end())));
```

#### Iteration object copy

Take care to not create copies of large objects while
iterating.

Suppose we have a vector of $N = 10$ large vectors:
```cpp
// each vector having 1M
vector<vector<int>> v;
```

We are given $Q = 10^5$ simple queries: given $i$,
output element at index $i$ for each vector.

Roughly this translates to $N$ operations per query.

Consider this code (for one query):
```cpp
for (auto x : v) cout << x[i] << '\n';
```

This will cause TLE because the local variable `x` is being created as copy of each
element in `v` which in this case is a large vector.

A subtle but important change prevents it - using reference:
```cpp
for (auto& x : v) cout << x[i] << '\n';
```

#### Modulus operator

The result of division of integers $a$ and $b$ ($b \neq 0$) can be expressed as:

$$
\frac{a}{b} = \lfloor \frac{a}{b} \rfloor + \frac{x}{b}
$$

where $x = a \pmod b$. Thus,

$$
a \pmod b = a - b \lfloor \frac{a}{b} \rfloor
$$

Examples:
- $-6 \pmod 4 = -6 - 4 \lfloor \frac{-6}{4} \rfloor = -6 - 4(-2) = 2$

- $6 \pmod {(-4)} = 6 - (-4) \lfloor \frac{6}{-4} \rfloor = 6 - (-4)(-2) = -2$

But in C++, these results will not match.
```cpp
int u = 6 % -4; // 2
int v = -6 % 4; // -2
```

C++ defines this operator as:

$$
a \pmod b = a - b \cdot f(\frac{a}{b})
$$

where $f(x)$ removes fractional part from $x$ (ignoring sign), i.e.,
$f(-2.3) = -2$ and $f(2.3) = 2$.

> For $x \ge 0$, $f$ is equivalent to floor function.

#### Avoid creating copies

Suppose we have a custom type `Element` and we create
an instance of it:
```
Element elem;
// do stuff with elem

vector<Element> elements;
elements.push_back(elem);
```

Now, is `elem` same as `elements[0]`? **No**, it is a
copy of it.

Use: `std::move` instead. Read docs.

```
elements.push_back(std::move(elem));
```

#### Random generator

```cpp
mt19937 rng(std::chrono::system_clock::now().time_since_epoch().count());
/* use rng() to get random 32-bit integer */
```

### Language specific: Python

#### Floating point precision problem
```py
import math
def f(x):
    return math.floor(math.log2(x))
    # 1125899906842623 = 2^50 - 1 gives 50 which is INCORRECT
    # 1125899906842624 = 2^50 gives 50
```

***