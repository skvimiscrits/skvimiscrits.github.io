---
layout: page
title: Bit manipulation - 1
parent: Programming
math: true
nav_order: 2
---

## Introduction

### Basics

Bit operations manipulate the binary representation of integers.
The fundamental operations are given below.

> Note: `and`, `or`, `xor` and `negation`
> apply on each bit indepedently.

|Operation|Type|Common symbol|Description|
|---|---|---|---|
| and | binary | `&` | If both bits are set then result $1$ else $0$
| or | binary | `\|` | Result is $1$ is at least one bit is set, otherwise $0$
| xor | binary | `^` | Result is $1$ if both bits are different, otherwise $0$
| negation | unary | `~` | Flips the bit
| left shift | binary | `<<` | Left shift by given number of times appending 0s. Loses that many bits at the leftmost end.
| right shift | binary | `>>` | Similar to left shift but towards right side.

### Common operations

> Note: A commonly used expression `(1 << i)` denotes an integer with a single
> set bit at position $i$. Numerically, it has value $2^i$.

Assume that the following operations are done on some
integer $x$.

|Operation|Expression|
|---|---|
|Set bit at position $i$ | `x = x \| (1 << i)` |
|Unset bit at position $i$ | `x = x & (~(1 << i))` |
|Flip bit at position $i$ | `x = x ^ (1 << i)` |
|Check if bit $i$ is $1$ | `(x & (1 << i)) != 0` |

### Representing sets

Bit representation of integers can be used to denote a set of elements.
Bitwise operations can make the operations on such sets practically very fast.

> Such a representation is commonly termed as *bit-masks*

A 4-byte (32-bit) integer can be used to represent a set of up to 32 fixed elements.
The presence of $i$-th element is denoted by bit $i$. If it is $1$, then the element is present.

Set related operations using bit:
- Union: using or
- Intersection: using and
- Complementary set: using negation
- Subset check: `y` is a subset of `x` if `(x | y) == x`
  - If `y` contains any bit not in `x`, then `(x | y) > x`.

To iterate through all possible ($2^n$) subsets of $n$ elements,
we can use a simple loop:

```cpp
for (int i = 0; i < (1 << n); i++) {
  // i is the subset or mask
  for (int j = 0; j < n; j++) if (i & (1 << j)) {
    // element j is present in this subset
  }
}
```

#### Iterating through subsets of an arbitrary set

Let integer `x` denote a subset of $n$ elements $0$ to $n-1$ (basically $n$ bit integer).
How to loop through all subsets of `x`?

The following example illustrates the problem better:
```
x = 01001
subsets (total 4 as 2 elements in x):
    00000
    00001
    01000
    01001
```

One can find the number of bits (let them be $k$)
and then iterate through $2^k$ subsets and reverse
mapping the bits to those in `x`.

There is an elegant way of doing this:
```cpp
for (int i = x; i > 0; i = (i - 1) & x) {
  // i is a subset of x
}
```

If $k$ bits are set, this loops has $2^k - 1$ iterations.

> Note: this loop does not consider the empty subset `i = 0`

Why does this work?
- Considering only the $k$ set bits in $x$, we iterate from $2^k-1$
  down to $0$
- Normally, `-1` works by converting the rightmost `100..00` section
  to `011..11`. In thise case, `i - 1` will cause the
  rightmost `1` in `i` (which is a sub-mask of `x`), to
  become `0` and everything to the right will be set to `1`.
  Now this is not a problem because `(i - 1) & x` will remove
  the unnecesarry 1s.
- Illustration:
  ```
  x = 01001
  i = 01001
      01000  # (01001 - 1) -> 01000 & 01001
      00001  # (01000 - 1) -> 00111 & 01001
      00000  # (00001 - 1) -> 00000 & 01001
  ```


## More operations

### Rightmost bit or least significant set-bit (LSB)

We can find the least significant set bit by
starting from lowest bit position and going up
until we find a set bit.

However, there exist efficient techniques to
deal with LSB.

To find LSB *mask* in a single operation do:
```
x & (-x)
```

How does it work?
- `-x` flips all the bits and then adds 1 to it (two's complement)
- In `x`, suppose LSB is at position $k$. Then in `-x`,
  when all bits are flipped, position $k$ will have $0$ and
  positions from $0$ to $k-1$ will have all $1$ bits.
  Obviously bits beyond $k$ will also be flipped.
  Upon adding $1$, the bit at position $k$ will become $1$
  and thus the `&` operation results into a single bit
  at position $k$ being set.
- Visualization:
  ```
  x        = 0001001010000
  -x       = 1110110110000
  x & (-x) = 0000000010000
  ```

> Note: LSB mask means the integer with only a single
> bit set which is LSB. If bit `i` is LSB, then LSB mask is `(1 << i)`
> it is fairly simple to go from bit position to mask
> but not from mask to bit position (you may need a mapping).

Now, to reset the LSB one can simply do:
```cpp
y = x & (-x)
x ^= y
```

There is a shorter way of doing this:
```
x &= (x - 1)
```

To understand how this works, consider the
section after LSB (included), it will be
of form `100...00`. In `x-1`, the bits above
LSB will be unaffected and this section will change
to `011...11`. Now upon the `&`, the LSB will be unset.

> Note: Both of these methods work when at least one bit is set.

### Counting number of set bits
- Trivial way: loop through all bits and count them.
- Language specific: inbuilt function (C++)

  ```cpp
  __builtin_popcount(x)
  ```
- By unsetting LSBs:
  ```cpp
  int count = 0;
  while (x != 0) {
    x &= (x - 1);
    count++;
  }
  return count;
  ```

### Leftmost bit or most significant set bit (MSB)

Given a $n$-bit integer, we need to find a mask which contains
the highest set bit.

For example, `00010011` will result in mask `00010000`

Trivial solution is checking all bits starting from highest possible.
But this runs in time $O(n)$.

#### Bit smearing technique

Idea:
1. Set all the bits to the right of the leftmost bit.
1. Do some operation to get rid of all 1s except for MSB

For first step, from the above example, we would have `00011111`.

To achieve this, we can do this step $n$ times:
```
x |= (x >> 1)
```

From the above example, if this operation is done:
- First time: `00010011 | 00001001 = 00011011`
- Second time: `00011011 | 00001101 = 00011111`
- and so on (since we will not know when to stop, we will do this $n-1$ times to cover all cases)

We can optimize this since after first `x |= (x >> 1)`, at least
2 leftmost bits will be set. Then, `x |= (x >> 2)` is guaranteed to
set 2 more leftmost bits totalling 4. We can follow through with this idea
to set next 4, 8, 16 (and so on) bits.

> Note that we do not assume anything about bits to right of MSB initially.

Thus in $k$ operations, we can set a total of $1 + 2 + \ldots + 2^k = 2^{k+1} - 1$ bits to the right of MSB.
Since maximum number of bits to right of MSB can be $n - 1$, we have $2^{k+1} - 1 \ge n - 1$
or $k = \lceil \lg{n} \rceil - 1$

Illustration:
```cpp
// x = 10000000 00000000
x = x | (x >> 1)
// x = 11000000 00000000
x = x | (x >> 2)
// x = 11110000 00000000
x = x | (x >> 4)
// x = 11111111 00000000
x = x | (x >> 8)
// x = 11111111 11111111
x ^= (x >> 1)
// x = 10000000 00000000
```

To get rid of all additional set bits at then end, we do `x ^= (x >> 1)`
(this works because in `(x >> 1)` MSB will be 0 and XOR will result in 1)


C++ code for 32-bit integer:
```cpp
int msb (int x) {
  x |= (x >> 1)
  x |= (x >> 2)
  x |= (x >> 4)
  x |= (x >> 8)
  x |= (x >> 16)
  return x ^ (x >> 1)
}
```

### Tricks

#### Swap two integers without a temporary variable

```
# given a, b
a' = a ^ b # marking a' to differentiate from original a
b' = b ^ a'  # which is (b ^ (a ^ b)) = a
a' = b' ^ a' # which is (a ^ (a ^ b)) = b

# written cleanly
a ^= b
b ^= a
a ^= b
```

#### Finding if an integer is a power of 2

Simply unset the LSB and number should become 0

```
x & (x - 1) == 0
```

#### Checking if two integers have opposite sign

This works because of the sign bit.

```
(a ^ b) < 0
```

#### Addition with bitwise operators

```
a + b = (a ^ b) + ((a & b) << 1)
```

> This definition is quite fundamental:
> - `(a ^ b)` denotes addition excluding carry.
> - `(a & b) << 1` denotes the carry.

Notice that the carry part always shifts the bits
one place to left. So, it will eventually overflow or
become 0.

Will this work for negative numbers?
- It does, but how?

***