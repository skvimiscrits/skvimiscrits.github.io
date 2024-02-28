# Heaps

Heaps are complete binary trees in which each node satisfies
a particular property.

In a **min-heap**, each node has value equal to minimum of
values of all nodes under subtree rooted at that node.
Similarly, in a **max-heap**, each node has maximum value
over all nodes under its subtree.

We will discuss min-heap. Same concept can be extended to max-heap.

## Representation

We can represent a complete binary tree containing $n$ using
an array $a$ of size $n$ (assuming $1$-indexed).

$a_i$ will denote the value of node at index $i$.

Recall that following holds for a complete binary tree of $n$ nodes represented through an array:
- Index $1$ represents root
- Index $i$ has left child $2i$ and right child $2i+1$
  - Right child exists of $2i+1 \le n$
  - Left child exists if $2i \le n$
  - If left child doesn't exists, then right child also doesn't exist. That
    means node at index $i$ is a leaf if $2i \gt n$.
- Parent of node at index $i$ (if $i \gt 1$) is $\lfloor \frac{i}{2} \rfloor$.

## Inserting new node

If we want to add a node with value $x$, we can add it
as a leaf at the lowest level, or create new level if
last level is fully filled (maintaining a complete
binary tree).

If current array size is $n$, it will become $n+1$.
We will set $a_{n+1} \leftarrow x$.

But after adding this node, the min-heap invariant might not
hold true for ancestor nodes of $n+1$ (notice that
**only** ancestor nodes are affected).

In the ancestor chain of newly added node, we might need to
shift values one node down to make place for newly added
value $x$. After this, going down from root to newly added
node will produce values in sorted order. 

This operation takes $O(\lg N)$ operations as there are $O(\lg N)$
nodes in ancestor chain of newly added node.

## Removing root

Node $1$, the root, will have the minimum value $a_1$ out of all nodes.

To remove this value, we can replace it with $a_n$ (the rightmost leaf of last level).
The tree now contains $n-1$ nodes.

But the heap invariant may not hold. Node $1$ has children $2, 3$.
If $a_1 = \min(a_1, a_2, a_3)$, then heap invariant is satisfied.
Otherwise, let $a_2$ be the minimum value (WLOG). Then we will swap values of $a_1$ and $a_2$.
Note that the subtree at node $3$ is still a min-heap. We can
recursively fix node $2$.

## Sorting

By using the concepts of addition and removing root, we
can sort a list of elements. Suppose we want to sort list
of $n$ integers $a_1,\ldots,a_n$.
- First build a min-heap of $a_1, \ldots, a_n$ by inserting
  one-by-one.
- Remove root (minimum) repeatedly to get elements in sorted order.

Both steps take $O(n \lg n)$.

## Implementation

Sample implementation of a min-heap:

```cpp
struct heap {
  vector<int> h;
  int n;
  heap() { h = vector<int>(1, 0); n = 0; }
  void insert (int x) {
    h.push_back(x); n++;
    for (int i = n; i > 1 && h[i / 2] > h[i]; i /= 2) {
      swap(h[i / 2], h[i]);
    }
  }
  int top() const {
    assert(n > 0);
    return h[1];
  }
  void pop() {
    assert(n > 0);
    h[1] = h.back();
    h.pop_back(); n--;
    for (int i = 1; 2 * i <= n; ) {
      int j = i, l = 2 * i, r = 2 * i + 1;
      if (l <= n && h[l] < h[j]) j = l;
      if (r <= n && h[r] < h[j]) j = r;
      if (j == i) break;
      swap(h[i], h[j]);
      i = j;
    }
  }
  int size() const { return n; }
};
```

Using min-heap to sort:

```cpp
vector<int> heap_sort (vector<int>& a) {
  heap h;
  for (auto x : a) h.insert(x);
  vector<int> res;
  while (h.size()) {
    res.push_back(h.top());
    h.pop();
  }
  return res;
}
```