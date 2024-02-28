## AVL tree (height balanced BST)

> A height balanced binary tree has the following true
> for all nodes:
> heights of left and right subtrees does not differ by more than 1

### Balance factor

Balance factor is defined for a node $x$ as:

$$
B(x) = H(x_r) - H(x_l)
$$

where $H(u)$ denotes height of a subtree rooted at $u$ and
$x_r, x_l$ are left and right children of $x$, respectively.

For a BST to be AVL tree, the following invariant holds for all
nodes:
$$
B(x) \in \{ -1, 0, 1 \}
$$

> This invariant is consistent with the height balanced
> tree definition above.

We will store this extra information for each node of the tree.

### Operations

Traversal and search are same as that of a regular BST.
We will discuss operations which can alter balance.

#### Insertion

First, the insertion takes place as in a normal BST.

This may impact balance factor of some nodes. Precisely,
let the inserted node be $x$ (which is a leaf).
Then *only* the nodes on path from root to $x$ may have their balance
factor changed as a result of this operation.

- Also, the balance factor changes by at most one

> TBD
> More precisely, let the path from root to $x$ be $u_1, \ldots, u_k$
> where $u_k = x$

We then start apply balancing operations from $x$ and move up towards root.

Balancing operations:

We will discuss few cases that can occur on insertion
and require balancing.

> Note: The leaf nodes in below diagram may depict `NULL`
> nodes, i.e., node with height $0$ (which is essentially a node not present).

Consider the following case where a node was inserted
somewhere in $v$


![](/assets/images/trees/balancing-1.png)

- Nodes in path to root (from the inserted node)
  were affected, i.e, $[r, u, v]$ (highlighted in red).
- The node under analysis is $r$. All other nodes $u, v, a, b$
  are height-balanced.
- Height of $r$ prior to insertion must have been $h+2$.
- The following order holds before and after the operation:
  $[v, u, b, r, a]$
- After this operation (in image below), we do not need to continue up
  to the path to root because the height of $r$
  becomes same as what it was before.


The other scenario is similar but involves a bit more shuffling
around to fix:

![](/assets/images/trees/balancing-2.png)

- $r$ is the target of this operation.
- The insertion was performed somewhere in $v$.
- The following order holds before and after the operation:
  $[b, u, x, v, y, r, a]$
- No futher operations are required because height of $r$
  is restored to its previous height.

There are two symmetric cases (for $u$ being right child of $r$)
which can be handled similarly.