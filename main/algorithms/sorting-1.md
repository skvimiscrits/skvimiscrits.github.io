---
layout: page
title: Sorting - 1
parent: Algorithms
math: true
nav_order: 2
---

## Introduction

Sorting is a very fundamental problem, acting as a building
block for other algorithms.

Given a sequence of $n$ elements $a_1, \ldots, a_n$
drawn from a *totally ordered set*, permute them into a sequence
$b$ satisfying $b_i \le b_{i+1}$ for all $1 \le i \lt n$.

> In simple terms, a totally ordered set or total order means
> that all elements are comparable with each other.


### Common terminology

- **In-place**: In-place sorting algorithms manipulate the input in its
  original container. Precisely, these require $O(1)$ extra space.
- **Stable sort**: A sorting algorithm which preserves the original order of
  elements in case of ties (in keys).
  > It is a common task to sort *objects*, based on defined keys.  An object
  > may contain other information, for example a unique identifier. In such
  > cases there is a clear distinction between stable sort and non-stable sort
  > algorithm output.
- **Comparison based sort**: A sorting algorithm which only uses comparisons
  between elements to determine the output. This is in contrast with algorithms
  which can use only the key of an element to get a hint about position in
  sorted order.


## Simple sorting algorithms

> In the following sections, we use C++ code for the algorithm.
> We pass the input array (or equivalent) to the method and
> it manipulates the input itself rather than returning a new sequence.
> This is a common practice.
>
> Also note that we use terms like sequence and arrays interchangably
> at some places.

### Insertion Sort

The idea of insertion sort is to gradually expand the sorted
part at start (or left) of the array until the whole array is sorted.

```cpp
void insertionSort (vector<int>& a) {
  int n = (int)a.size();
  for (int i = 0; i < n; i++) {
    int x = a[i], j = i - 1;
    while (j >= 0 && a[j] > x) {
      a[j + 1] = a[j];
      j--;
    }
    a[j + 1] = x;
  }
}
```

Loop invariant for the outer loop is: At the end of the an iteration (loop
variable $i$), $a_0, \ldots, a_i$ are in sorted order and they contain elements
originally in $a_0, \ldots, a_i$.

- **Base case**: This is true for the first iteration ($i = 0$) since it will
  have no effect on the array and a single element is always sorted.

- **Propagation**: Suppose this is true for iteration $i-1$, then at the start
  of iteration $i$, $a_{0}, \ldots, a_{i-1}$ are in sorted order and they
  contain elements present originally in that range. Now, the while loop will
  shift the $a_{i}$ (stored in $x$) until it reaches its correct position,
  hence making $a_{0}, \ldots, a_{i}$ sorted and containing elements present
  originally in that range.

- **Termination**: At the end of last iteration ($i = n-1$), the elements
  $a_{0}, \ldots, a_{n-1}$ will be in sorted order.

Let us analyse the running time of insertion sort. The outer `for` loop is
guaranteed to run $n$ times, however, the number of times the inner `while`
loop depends on the value of input.

- **Best case**: If the inner loop runs $0$ times in each iteration, then we
  have running time $\Theta(n)$.  This will happen when $a_{j} > x$ is never
  satisfied, i.e, the input is sorted.

- **Worst case**: If the inner loop runs maximum number of times ($i$ times),
  then we have running time $\Theta(n^2)$ (by summing up $0 + 1 + \ldots +
  n-1$).

We can't provide a tight bound on the running time of insertion sort since the
lower bound is $\Omega(n)$ (lower bound of best case running time) and the
upper bound is $O(n^2)$ (upper bound of worst case running time).

> Note:
> - The statement "In the worst case, Insertion sort runs in
>   $\Omega(n^2)$" is correct since
>   $T_{\text{worst}}(n) = \Theta(n^2) \Rightarrow T_{\text{worst}}(n) = \Omega(n^2)$.
> - Similary, the statement
>   "In the best case, Insertion sort runs in $O(n)$"
>   is also correct.

### Bubble Sort

The idea of bubble sort is similar to that of insertion sort,
gradually expanding the sorted part at the left. However, it
works by shifting the minimum element from the rest of the array
to the end of the sorted part.

```cpp
void bubbleSort (vector<int>& a) {
  int n = (int)a.size();
  for (int i = 0; i < n; i++) {
    for (int j = n - 1; j > i; j--) {
      if (a[j] < a[j - 1]) swap(a[j], a[j - 1]);
    }
  }
}
```

Loop invariant for the outer `for` loop is: At the end of the an iteration
(loop variable $i$), $a_{0}, \ldots, a_{i}$ are in sorted order and there
exists no element $a_{j} < a_{i}$ for $i < j < n$.

Note how the statement in the inner `for` loop shifts the minimum of two
adjacent elements to the left. Since this loop starts from the end of the
array, at the start of an iteration (loop variable $j = k$), $a_{k}$ contains
the minimum of $a_{k}, \ldots, a_{n - 1}$. This can be proved by simple
induction: Suppose this is true for some $j = k$ (base case: for $j=n-1$ this
is true), then this will hold true for $j = k-1$ since the comparison between
$a_{k}$ and $a_{k-1}$ will take place and the smaller of them will reside in
$a_{k-1}$.

- **Base case**: After the execution of the inner for loop, $a_{0}$ will
  contain the minimum element of the array and a single element is always
  sorted.

- **Propagation**: Suppose this is true for iteration $i-1$, then at the start
  of iteration $i$, $a_{0}, \ldots, a_{i-1}$ are in sorted order and there is
  no such element $a_{j}$ such that $a_{j} < a_{i-1}$ for $i-1 < j < n$.
  Suppose the minimum element in $a_{i}, \ldots, a_{n-1}$ is $x$. Then, we have
  $a_{i-1} \leq x$. After the inner loop executes, the minimum element from
  this range is brought to $a_{i}$, i.e, $a_{i} = x$. Thus, $a_{0}, \ldots,
  a_{i}$ are in sorted order.

- **Termination**: At the end of last iteration ($i = n-1$), the elements
  $a_{0}, \ldots, a_{n-1}$ will be in sorted order.

Let us analyse the running time of bubble sort. The outer
*for* loop runs $n$ times, with the inner loop running a
total of

$$
(n-1) + (n-2) + \ldots + 1 + 0 = \Theta(n^2)
$$

times.
Assuming comparison and swap takes constant time, we have
running time $\Theta(n^2)$. We get a tight bound for the running
time of Bubble sort. Note that the running time of bubble sort
is **independent** of the input unlike insertion sort.

### Selection Sort

Selection sort is very similar to bubble sort. The only
difference is that bubble sort repeatedly swaps elements to
bring the minimum to the end of the sorted part while the
selection sort first finds the minimum element and performs a
single swap to place it on the end of the sorted part.

```cpp
void selectionSort (vector<int>& a) {
  int n = (int)a.size();
  for (int i = 0; i < n; i++) {
    int k = i;
    for (int j = i + 1; j < n; j++) {
      if (a[j] < a[k]) k = j;
    }
    swap(a[i], a[k]);
  }
}
```

Loop invariant for the outer *for* loop is same as that of bubble sort: At the
end of the an iteration (loop variable $i$), $a_{0}, \ldots, a_{i}$ are in
sorted order and there exists no element $a_{j} < a_{i}$ for $i < j < n$.

After the inner loop executes, $k$ holds the index of the minimum element in
$i, \ldots, n - 1$. After the swap operation, $a_{i}$ becomes the minimum of
$a_{i}, \ldots, a_{n-1}$.

- **Base case**: After the execution of the inner for loop and swap operation,
  $a_{0}$ will contain the minimum element of the array and a single element is
  always sorted.

- **Propagation**: Suppose this is true for iteration $i-1$, then at the start
  of iteration $i$, $a_{0}, \ldots, a_{i-1}$ are in sorted order and there is
  no such element $a_{j}$ such that $a_{j} < a_{i-1}$ for $i-1 < j < n$.
  Suppose the minimum element in $a_{i}, \ldots, a_{n-1}$ is $x$. Then, we have
  $a_{i-1} \leq x$. After the inner loop executes, we have $a_{k} = x$, the
  minimum element. After the swap operation, $a_{i} = x$. Thus, $a_{0}, \ldots,
  a_{i}$ are in sorted order.

- **Termination**: At the end of last iteration ($i = n-1$), the elements
  $a_{0}, \ldots, a_{n-1}$ will be in sorted order.

The running time of selection sort is same as that of bubble sort. The outer
*for* loop runs $n$ times, with the inner loop running a total of $(n-1) +
(n-2) + \ldots + 1 + 0 = \Theta(n^2)$ times.  Assuming comparison and swap
takes constant time, we have running time $\Theta(n^2)$. We get a tight bound
for the running time of selection sort. The running time of selection sort is
**independent** of the input just like bubble sort.

Selection sort is a bit more efficient in practice than bubble sort because
swap operation takes more time than assignment (`swap(a[j], a[j-1])` in bubble
sort compared to `k = j` in selection sort).

## Bucket based sorting

Suppose you are asked to sort an integer sequence which contains only $1$'s and
$2$'s, a simple answer may be: simply count them, then first put all $1$'s and
then $2$'s in the output sequence.

If the keys of the input can be placed in reasonably small number of (ordered)
buckets, we can use very efficient algorithms to sort them.

### Counting sort

Couting sort is suitable for sorting integer keys lying within a small range
(usually not larger than $10^7$). It is a $O(n)$ **non-comparative** sorting
algorithm.

The idea is to first populate the frequency array, then compute prefix sums to
determine starting position of each element.  In the second pass, each element
is placed in its correct position.  The following example illustrates the idea:
```
Input: [(2, a), (3, b), (1, c), (2, d), (1, e)]
Assume first element of pair as key

Frequency array: [ 2 2 1 ]
          index:   1 2 3

After prefix sum,
Frequency array: [ 2 4 5 ]

Second pass (backwards):
--------------------------------------------------
original [(2, a), (3, b), (1, c), (2, d), (1, e)]
                                           ^
    freq [ 2 4 5 ] gives position 2 -> [ 1 4 5 ]
           ^
  output [      , (1, e),       ,       ,       ]
                   ^
--------------------------------------------------
original [(2, a), (3, b), (1, c), (2, d), (1, e)]
                                   ^
    freq [ 1 4 5 ] gives position 4 -> [ 1 3 5 ]
             ^
  output [      , (1, e),       , (2, d),       ]
                                   ^
--------------------------------------------------
original [(2, a), (3, b), (1, c), (2, d), (1, e)]
                           ^
    freq [ 1 3 5 ] gives position 1 -> [ 0 3 5 ]
           ^
  output [(1, c), (1, e),       , (2, d),       ]
           ^
--------------------------------------------------
original [(2, a), (3, b), (1, c), (2, d), (1, e)]
                   ^
    freq [ 0 3 5 ] gives position 5 -> [ 0 3 4 ]
               ^
  output [(1, c), (1, e),       , (2, d), (3, b)]
                                           ^
--------------------------------------------------
original [(2, a), (3, b), (1, c), (2, d), (1, e)]
           ^
    freq [ 0 3 4 ] gives position 3 -> [ 0 2 4 ]
             ^
  output [(1, c), (1, e), (2, a), (2, d), (3, b)]
                                           ^
--------------------------------------------------
```

Counting sort is not in-place, it requires $O(n)$ auxilliary
space (the output array). However, it is a **stable sort**.

Implementation in C++:
```cpp
void countingSort (vector<int>& a) {
  const int MAX_VAL = 1e5 + 1;
  vector<int> freq(MAX_VAL, 0);

  for (auto x : a) freq[x]++;
  for (int i = 1; i < freq.size(); i++)
    freq[i] += freq[i - 1];
  vector<int> b (a.size());
  for (int i = a.size() - 1; i >= 0; i--) {
    b[--freq[a[i]]] = a[i];
  }
  a = b;
}
```

Notes:
- The above code works for integer range $[0, 10^5]$ but it can be adjusted to
  work with an arbitrary range (range size should be reasonably small) by using
  an offset for frequency array.
- To adjust for 0-index, `--freq[a[i]]` is used rather than `freq[a[i]]--`.

### Radix sort

Radix sort is used to sort *words* (assume fixed length for convenience)
lexicographically.  A word can be a string or an integer (made fixed length by
appending 0s to left).

The idea behind Radix sort is performing multiple passes of a stable sort on
each position of a character using buckets.  Mainly there are two variants:
least significant digit (LSD) and most significant digit (MSD) radix sort.

We will take an example of MSD radix sort:
```
Input: ["abc", "bca", "bac", "aab", "aba", "bbb"]

pass 1:
{"abc", "aab", "aba"} {"bca", "bac", "bbb"}

pass 2:
{"aab"} {"abc", "aba"} {"bac"} {"bbb"} {"bca"}

pass 3:
{"aab"} {"aba"} {"abc"} {"bac"} {"bbb"} {"bca"}
```

A simple implementation of MSD radix sort can be:
- Divide input elements into buckets based on MSD
- Sort elements in each bucket recursively using next MSD. Finally

For $n$ elements, with word length $w$ and $k$ buckets,
the time complexity is $O(w(n+k))$.

## Lower bound on sorting

For comparison based sorting algorithms, what is the best time complexity that
can be achieved?

A (non-parallel) algorithm does a series of comparisons to achieve at the final
result. How many comparisons are required at least?

Let there be $n$ elements $a_1,\ldots,a_n$.
- There are a total of $n!$ possible answers (permutations) based on the
  comparison results.
- Depending on the result of the comparison, the set of possible answers is
  divided into two sets. It is obvious that these two sets will be disjoint.
- We will always assume that we end up with the set which is not smaller than
  the other.
- After how many comparisons do we end up with a single permutation?

We can now think of it as a binary tree with $n!$ leaves.  We can also call
this a *decision tree* for the algorithm.  Each node denotes a comparison and
has two children, corresponding to the result of comparison. The best algorithm
will have minimum height of this tree (so that in the worst case, minimum
number of nodes are present in root to leaf path).

To find minimum height of a binary tree with $n!$ leaves,
we need minimum $h$ such that $2^h \ge n!$

$\Rightarrow h \ge \lg{n!}$

$\Rightarrow h \ge \lg{n} + \lg{(n-1)} + \ldots + \lg{2}$

$\Rightarrow h \ge \sum\limits_{2 \le i \le n}{\lg{i}}$

$\Rightarrow h \ge \sum\limits_{2 \le i \le \frac{n}{2} - 1}{\lg{i}} + \sum\limits_{\frac{n}{2} \le i \le n}{\lg{i}}$

$\Rightarrow h \ge 0 + \sum\limits_{\frac{n}{2} \le i \le n}{\lg{\frac{n}{2}}}$

> Note: In the last statement, we have simplified the terms by
> taking a lower value which is acceptable for proving a lower bound on $h$

$\Rightarrow h \ge \frac{n}{2} \lg{\frac{n}{2}}$

$\Rightarrow h = \Omega(n \lg{n})$

We have shown that no comparison based algorithm can run
faster than $O(n\lg{n})$ in worst case.

***
