---
layout: page
title: Recursive procedures - 1
parent: Algorithms
math: true
nav_order: 4
---

## Analyzing recursive procedures

In this section we see how some problems can be solved using
**recursion** (the process in which the procedure calls
itself with possibly different conditions/state). Two common
problem solving paradigms associated with recursion are
**divide and conquer** and **recursive backtracking**.

**Divide and conquer**

The idea of divide and conquer is:

- **Divide** the problem into subproblems and solve
them recursively.
- **Combine** the solutions of the subproblems to solve
the complete problem.
- **Base case** of the recursion should be defined so
that there is no recursion for a particular problem size.

We can not apply divide and conquer to *all* the problems
that we encounter. For example, we can't apply this strategy
when subproblems are dependent on each other.

To analyse the time complexity of a recursive procedure, we
first write the recurrence of the running time $T(n)$ (where $n$
is the input size). For a recursive procedure which divides the
problem of input size $n$ into $a$ subproblems (with $a \geq 1$)
of input size $\frac{n}{b}$ (with $b > 1$) and combines them in
$f(n)$ time, we have the recurrence:

$$
T(n) = a T(\frac{n}{b}) + f(n)
$$

To analyse the running time $T(n)$, we do not necessarily need a
closed form. Obtaining a tight bound $g(n)$ (meaning
$T(n) = \Theta(g(n))$) is sufficient. Moreover, finding closed form for
recurrences of this kind may not be trivial.

### Recursion Tree method

To find the running time of a recursive procedure, we can draw
the recursion tree for the same. Each node represents a call to
the recursive procedure. The root node is where the recursion
starts off. For each recursive call from a node, we have a child
node. Each node of the tree is marked with the time taken in
that node excluding the recursive calls. The overall running
time is the sum of running time of all the nodes.

### Master Method
The master method can be applied to recurrences of kind

$$
T(n) = a T(\frac{n}{b}) + f(n)
$$

where $a \geq 1$ and $b > 1$ (however, there are some cases
where it can't be applied).

We have $T(n) =$

- $\Theta(n^{\log_b a})$ when $f(n)$ is polynomially smaller
than $n^{\log_b a}$.

- $\Theta(n^{\log_b a} \lg n)$ when $n^{\log_b a}$ is an
asymptotically tight bound for $f(n)$.

- $\Theta(f(n))$ when $f(n)$ is polynomially larger than
  $n^{\log_b a}$ and satisfies the regularity condition:
  $c f(n) \geq a f(\frac{n}{b})$ for some constant $c < 1$ and for all
  sufficiently large $n$ (for all $n \geq n_0$ where $n_0$ is some
  constant)

The third case denotes the scenario where root node (in the
recursion tree) dominates the the running time asymptotically,
i.e, $T(n) = \Theta(f(n))$
Intuitively, the regularity condition is there to make sure that
the root node actually does more work than the next level in the
recursion tree (which is $a f(\frac{n}{b})$).

The proof of the master theorem is omitted here.

*Note:* A function $f(n)$ is polynomially smaller than a function $g(n)$
if $f(n)$ is asymptotically smaller than $g(n)$ by a factor of
$n^\epsilon$ for some $\epsilon > 0$, i.e,
$f(n) = O(g(n) / n^\epsilon)$.

Similarly, $f(n)$ is polynomially larger than $g(n)$ if
$f(n) = \Omega(g(n) n^\epsilon)$.

A tricky example is: $f(n) = n \lg n$ and $g(n) = n$. In this
case $f(n)$ is **not** polynomially larger than $g(n)$
because $\lg n$ is asympotically smaller than $n^\epsilon$ for
any $\epsilon > 0$.

Some examples of applying master method:

- $T(n) = 2 T(n/4) + \sqrt{n}$

  We have $a = 2, b = 4 \Rightarrow n^{\log_b a} = n^{1/2}$
  and $f(n) = n^{1/2}$. This is the second case, thus:
  $T(n) = \Theta(\sqrt{n} \lg n)$.

- $T(n) = 2 T(n/4) + n$

  We have $a = 2, b = 4 \Rightarrow n^{\log_b a} = n^{1/2}$
  and $f(n) = n$. This is the third case, thus:
  $T(n) = \Theta(n)$.

- $T(n) = 4 T(n/2) + n^2 \lg n$

  We have $a = 4, b = 2 \Rightarrow n^{\log_b a} = n^2$
  and $f(n) = n^2 \lg n$. Note that $n^2 \lg n$ is not
  polynomially larger than $n^2$. Also,
  $n^2 \lg n \neq \Theta(n^2)$. This recurrence can not be solved by the master
  method.

### Merge Sort

Merge sort is based on divide and conquer strategy. A simple
(and quite common) variation of merge sort is dividing the array
into two parts, recursively sorting them and combining them to
obtain the result.

While combining the two sorted subarrays, auxiliary arrays are
used which makes the space requirements for the algorithm to be
$O(n)$ (excluding the array to be sorted).

```cpp
void merge (vector<int>& a, int l, int r) {
  int m = (l + r) / 2;
  vector<int> u, v;
  for (int i = l; i <= m; i++) u.push_back(a[i]);
  for (int i = m + 1; i <= r; i++) v.push_back(a[i]);
  int u_itr = 0, v_itr = 0;
  for (int i = l; i <= r; i++) {
    if (u_itr == (int)u.size()) a[i] = v[v_itr++];
    else if (v_itr == (int)v.size()) a[i] = u[u_itr++];
    else a[i] = (v[v_itr] < u[u_itr] ? v[v_itr++] : u[u_itr++]);
  }
}

void mergeSort (vector<int>& a, int l, int r) {
  if (l == r) return;
  int m = (l + r) / 2;
  mergeSort(a, l, m);
  mergeSort(a, m + 1, r);
  merge(a, l, r);
}

void mergeSort (vector<int>& a) {
  mergeSort(a, 0, (int)a.size() - 1);
}
```

(the `mergeSort` procedure is overloaded to simplify the
call)

Let us analyse the time complexity of recursive procedure
`mergeSort`. The input size $n$ is $r - l + 1$. Let us
denote the running time of `merge` procedure by $f(n)$.
Then, the running time of `mergeSort` is
$T(n) = 2 T(n/2) + \Theta(1) + f(n)$. The $\Theta(1)$ term denotes the constant time
operation(s) before the recursive call.

`merge` has 3 loops apart from some
constant time operations (assignments, declarations, etc.). The
first two loops run for $n$ iterations ($n = r - l + 1$).
The third loop runs for $n$ iterations (again). We
have a total of $2n$ iterations which implies the overall
running time of this procedure is $\Theta(n)$.

Thus, we have $T(n) = 2 T(n/2) + \Theta(1) + \Theta(n)$ which
can be simplified to

$$
T(n) = 2 T(n/2) + \Theta(n)
$$

We can apply master method to obtain the tight bound for $T(n)$.
We have $a = 2, b = 2 \Rightarrow n^{log_2 2} = n$. This is the
second case, thus $T(n) = \Theta(n\lg n)$. This running time is
independent of input, i.e., there are no best or worst cases.
