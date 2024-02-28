# Binary Trees

A binary tree is a rooted tree in which each node has at most 2 children.

Usually the children nodes are called **left** child and **right** child (if present).

### Representation

With a linked-list style representation, each node has a left and right child
```cpp
struct Node {
  Node* left;
  Node* right;
  int val;
};
```

## Binary search trees (BST)

Binary trees in which additional constraint is present on the value of the nodes (the values should be comparable)
- For a node with value $x$, **all** nodes in its left subtree must have value lesser than $x$ and **all** nodes in right subtree must have a
  value greater than $x$.

> Normally we deal with distinct values only in a BST but
> if non-distinct values are taken into account, we can modify the constraint to have left child's value lesser than
> **or equal** to $x$.

To check if a BST is valid:
- Its left and right subtrees should be valid
- Max element in its left subtree must be lesser than root value
- Min element in its right subtree must be greater than root value

Sample code to check if a BST is valid:
```cpp
/* return {isValid, min, max} */
tuple<bool, int, int> check(TreeNode* node) {
  int mn = node->val, mx = node->val;
  if (node->left != nullptr) {
    auto r = check(node->left);
    if (!get<0>(r) || get<2>(r) >= node->val) return {false, 0, 0};
    mn = min(mn, get<1>(r));
  }
  if (node->right != nullptr) {
    auto r = check(node->right);
    if (!get<0>(r) || get<1>(r) <= node->val) return {false, 0, 0};
    mx = max(mx, get<2>(r));
  }
  return {true, mn, mx};
}
```

### Fast searches

The main utility of BSTs is searching in $O(H)$ time where $H$ is the height of the tree.
This is done by starting from root and going into the appropriate branch based on root value.
In each step, we descend one level (until the desired value is found). The max number of steps is $H$

### Common properties

- Minimum element can be found by starting from root and following the left child.
- For maximum, follow the right child.

### Traversals

TODO: Iterative versions of these

**Pre-order traversal**

Visit a node first, then its subtrees
Recursive definition:
- Process current node
- Pre-order traversal of left child
- Pre-order traversal of right child

**In-order traversal**

In order traversal for a tree means traversing the left subtree, then
root and then the right subtree.
The simple recursive definition for in-order traversal is:
- In-order traversal of left child
- Process current node
- In-order traversal of right child

> In case of BST, in-order traversal gives sorted order of elements

```cpp
void trav (Node* node) {
  if (node == null) return;
  trav(node->left);
  process(node);
  trav(node->right);
}
```

**Level order traversal**

Level order traversal means visiting levels incrementally starting from $0$ and
within each level, traversing nodes from left to right.

> Levels are defined as:
> - Root is at level $0$
> - If a node is at level $i$, its children will be on level $i+1$.

Breadth-first search (BFS) gives the level order traversal.

Sample code (here we are returning values for each level separately):
```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
  vector<vector<int>> res;
  if (root == nullptr) return res;
  vector<int> cur;
  int level = 0;

  queue<pair<TreeNode*, int>> q; /* store {node, level} instead of just node*/
  q.push({root, 0});

  while (!q.empty()) {
    auto p = q.front(); q.pop();
    if (p.second > level) { res.push_back(cur); cur.clear(); }
    level = p.second;
    TreeNode* node = p.first;
    cur.push_back(node->val);
    if (node->left != nullptr) q.push({node->left, level + 1});
    if (node->right != nullptr) q.push({node->right, level + 1});
  }
  if (cur.size() != 0) res.push_back(cur);
  return res;
}
```

If asked for lowest to highest level:
- One way is to reverse the final result.
- Do some fancy stuff like using linked-lists or doubly-ended queue so that
  you don't have to reverse (*try-hard solution*)


## Problems

### Unique BSTs for set of elements

Finding structurally unique BSTs for given set of elements (here integers $1,\ldots,N$)

> What is structurally unique? Can two BSTs with same structure and made from same set of elements have different values in some node?
> 
> If all elements are distinct then it is not possible. Suppose left child of root node has $n$ elements then root will necessarily have the $n+1$-th element in the sorted order.

Here we can choose a root and generate its children with the appropriate elements.
If the $i$-th element is chosen to be the root, then elements $1,\ldots,i-1$ will be
present in the left child and elements $i+1,\ldots,N$ will be present in the right child.

This particular sample code is simpler because it deals with integers $1...n$
```cpp
vector<TreeNode*> gen(int a, int b) {
  if (a > b) return {nullptr};
  vector<TreeNode*> res;
  for (int i = a; i <= b; i++) {
    vector<TreeNode*> left = gen(a, i - 1);
    vector<TreeNode*> right = gen(i + 1, b);
    for (auto l : left) for (auto r : right) {
      res.push_back(new TreeNode(i, l, r));
    }
  }
  return res;
}
```

We simply call `gen(1,n)`.

*Note: We are not analysing node re-usage in the above code sample (references / pointers). All generated trees are not wholly separate copies*

Before analysing time complexity of the above code sample, we need the
number of unique such BSTs with $n$ elements. Let $f(x)$ be the number of BSTs that can be formed
with $x$ elements.

$$
f(0) = 1
$$

For base case we have $f(0) = 1$ because with zero nodes we have a single tree with `null` root.

$$
f(x) = \sum_{0 \le i \le x-1}{f(i)f(x-1-i)}
$$

which is the famous **catalan numbers** recurrence relation.

> Catalan numbers are given by:
> $$
> C_n = \frac{1}{n+1} \binom{2n}{n}
> $$
> 
> and satisfy the recurrence relation:
> $$
> C_{n+1} = C_0C_n + C_2C_{n-1} + \ldots C_nC_0
> $$ 

Thus,

$$
f(x) = \frac{1}{x+1} \binom{2x}{x}
$$

The time complexity of above code sample is $O(f(n))$.

Catalan numbers can be computed fast using memoization. Sample code (assume `f` to be an array having `-1`s initially):
```cpp
int calc(int n) {
  int& ans = f[n];
  if (ans != -1) return ans;
  if (n == 0) return ans = 1;
  ans = 0;
  for (int i = 0; i < n; i++) {
    ans += calc(i) * calc(n - 1 - i);
  }
  return ans;
}
```

Since calculation for each $n$ will be done only once, we will get
overall number of operations $1 + 2 + \ldots + n$ resulting in $O(n^2)$ time (assuming other operations like multiplication taking constant time).

### Recover BST with two swapped node values

If two values are swapped, the binary tree will no longer be a BST.
This is equivalent to swapping two elements in a sorted list of distinct elements (which is the in-order traversal of BST).

Suppose the list is $a_1,\ldots,a_n$ and $a_i, a_j$  (with $i \lt j$) are swapped, then **first** $k$ for which $a_k \gt a_{k+1}$ will give $i = k$
and **last** $k$ where $a_k \gt a_{k + 1}$ will give $j = k + 1$.

Thus, an $O(n)$ extra space solution is obtaining this list and finding the swapped elements and then
swapping those node values in BST.

A more space efficient $O(1)$ solution is storing last processed node during in-order
traversal and by comparing current node value with last node value, we can find
the swapped nodes with the above logic (first and last $k$ with $a_k \gt a_{k+1}$).

Sample code:
```cpp
TreeNode* a; /* i as described above */
TreeNode* b; /* j as described above */
TreeNode* prev; /* last processed node */

void process(TreeNode* node) {
  if (prev != nullptr && prev->val > node->val) {
    if (a == nullptr) a = prev; /* always first one */
    b = node; /* always last one */
  }
  prev = node;
}

void inorder(TreeNode* node) {
  if (node == nullptr) return;
  inorder(node->left);
  process(node);
  inorder(node->right);
}

void recoverTree(TreeNode* root) {
  a = b = prev = nullptr;
  inorder(root);
  swap(a->val, b->val);
}
```

## Recover tree from inorder and preorder traversal

Note: All elements are distinct

Some observations:
- Preorder traversal $P$ has form $[t, L_1,\ldots, L_p, R_1, \ldots, R_q]$
  where $t$ is the root and sequences $L$ and $R$ are preorder traversals
  of left and right subtrees respectively.
- Inorder traversal $I$ has form $[L'_1,\ldots, L'_p, t, R'_1, \ldots, R'_q]$
  where $t$ is the root and sequences $L'$ and $R'$ are inorder traversals
  of left and right subtrees respectively.

Since $t$ can be known from preorder traversal, we can find $L'$ and $R'$
by searching for $t$ in inorder traversal.
This gives us $p = \vert L' \vert$ and thus first $p$ elements after $t$
in preorder traversal will give us $L$.

Use this recursively to recover the tree. Pseudocode:
```
def recover(P, I):
  if P.size() == 0: return NIL
  root = new Node(P[1])
  k = I.find(P[1]) # Finding root index O(N) operation
  root.l = recover(P[2..k], I[1..k-1])
  root.r = recover(P[k+1..], I[k+1..])
  return root
```

The $O(N)$ operation for finding index of root in inorder traversal
makes this algorithm run with time $O(N^2)$.

Can we optimize?

One way is to use unordered map ($O(1)$ search) to keep
mapping of value to index for $I$ and use that to
replace `k = I.find(P[1])`

Another way is this:
Suppose we are processing some subtree whose inorder and preorder traversal
start from $P_u$ and $I_v$. If this subtree has $k$ nodes, then (since the
nodes will be occurring as a contiguous sublist):
- Preorder traversal is $P_u, \ldots, P_{u + k - 1}$
- Inorder traversal is $I_v, \ldots, I_{v + k - 1}$

Now suppose we change the recursive method `recover` above to take
$P, I, u, v$ and return the recovered subtree as well as size $k$ of the
subtree.

But we need to stop recursion on leaves. A simple check such as $P_u = I_v$ will not work.
We need a way to indicate when to stop processing the current
subtree. Since inorder traversal has form $[L, t, R]$ (where $t$ is root,
$L, R$ are inorder traversals of left and right subtrees respectively),
we can say for left subtree (traversal $L$) that we should stop before $I_j = t$.
For $R$ we can add a sentinel value at the end of inorder traversal initially (for simplification).
Thus we add another parameter to indicate **end** called $e$.

```
def recover(P, I, u, v, e): # Returns (root, size)
  if I[v] == e:     # Empty / absent subtrees
    return (NIL, 0)
  root = new Node(P[u])
  if I[v + 1] == e:     # Leaf
    return (root, 1)
  L, x = recover(P, I, u + 1, v, P[u])
  R, y = recover(P, I, u + 1 + x, v + 1 + x, e)
  root.l = L
  root.r = R
  return (root, 1 + x + y)

# Answer
recover(P, I + [C], 0, 0, C) # C is some sentinel value
```

This algorithm is $O(N)$.

An important part is `L, x = recover(P, I, u + 1, v, P[u])`
where we use the fact that inorder traversal of left subtree
starts from same index as inorder traversal of whole tree. Also notice
the end $e = P_u$ (in inorder traversal) is the present root.

> Note: If we wish not to use sentinel values, then add length based
> conditions where $e$ is used. 

**TODO: Without recursion**

Find explanation for this solution:
```
S = []
root = Node(P[1])
j = 1
for i = 2 to N+1:
  if I[j] != S.top():
    t = Node(P[i])
    u = S.top()
    u.left = t
    S.push(t)
  else:
    u = NIL
    while S.size() > 0 and S.top() == I[j]:
      u = S.top()
      S.pop()
      j += 1
    if j <= N:
      t = Node(P[i])
      u.right = t
      S.push(t)
# root is the answer
```

## Recover tree from inorder and postorder traversal.

Inorder traversal has form:
```
[ ...L, t, ...R ]
```

Postorder traversal has form:
```
[ ...L, ...R, t ]
```

We can use same concept as above:

Use unordered map to store mapping of value to index of inorder traversal
to identify subtree sections

Sample solution:
```cpp
vector<int> I, P;
map<int, int> pos;

TreeNode* bt (int l1, int r1, int l2, int r2) {
  if (l1 > r1) return nullptr;
  TreeNode* root = new TreeNode(P[r2]);
  int k = pos[P[r2]];
  root->left = bt(l1, k - 1, l2, l2 + (k - l1) - 1);
  root->right = bt(k + 1, r1, r2 - (r1 - k), r2 - 1);
  return root;
}

TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
  I = inorder;
  P = postorder;
  for (int i = 0; i < I.size(); i++) pos[I[i]] = i;
  return bt(0, I.size() - 1, 0, P.size() - 1);
}
```

***
***


- Each node in BST represents a range which is disjoint from any other
  range of a node at the same level

## Balanced BST from sorted list

> A height balanced BST satisfies the condition:
> Children of each node should not have heights
> differing by more than one.

Given a list of $n$ integers $a$, assume $f(a)$ produces a balanced BST.

Then, we can recursively define $f(a)$ as:
- Base case ($n = 0$):
  - $a$ has no element: produce empty tree (balanced)
- Assume $a$ to be sorted.
  - Choose element at index $\lfloor \frac{n}{2} \rfloor$
    to be the root $r$. Let elements to smaller than this
    be in sub-list $u$ and larger be in $v$.
    $u$ and $v$ will differ in length by at most $1$.
  - Obtain $f(u)$ and $f(v)$ and have them as left and right
    child of $r$. Return $r$.
  - We will show that trees produced $f(u)$ and $f(v)$ will
    also differ in height by at most $1$.

*Claim: Let $g(x)$ be the height of tree $f(a)$ when $\vert a \vert = x$
 then it holds that $0 \le g(x + 1) - g(x) \le 1$*

Firstly, this holds for $x = 0$ because
$g(0) = 0$ and $g(1) = 1$.

Now, assuming that this holds for $i = 0, \ldots, x-1$
we will show that this holds for $i = x$.

Let $x = 2p + 1$ (for $p \ge 0$)
- It will have both children of height $g(p)$. Thus, $g(x) = g(p) + 1$.
- For $x - 1 = 2p$, there will be children with heights
  $g(p)$ and $g(p-1)$ which means $g(x - 1) = g(p) + 1$.
- Thus, $g(x) = g(x - 1) = 1 + g(p)$ in this case.
- In other words $2p+1$ and $2p$ result into tree with same heights.

Let $x = 2p + 2$ (for $p \ge 0$)
- It will have children of heights $g(p+1)$ and $g(p)$ resulting in $g(x) = g(p+1) + 1$.
- For $x - 1 = 2p+1$, we have $g(x - 1) = 1 + g(p)$ from above.
- Since $0 \le g(p + 1) - g(p) \le 1$

  $\Rightarrow 0 \le (1 + g(p + 1)) - (1 + g(p)) \le 1$

  $\Rightarrow 0 \le g(x) - g(x - 1) \le 1$

## Height of binary tree from arbitrary root

Relevant for left/right pointer representation.
For a general representation, it is very simple to find the height.

For node R as root, the height is max of
- height of left/right subtrees
- for direct parent: 1 + height of other child
- for parent of parent: 2 + height of other child and so on..

Precisely:
- let the path from root to R be p_1,...,p_k (p_1 is root and p_k = x)
- let q_1,...,q_{k-1} be the 'other' children in the path
  meaning that on p_i, p_{i+1} was the next selection and
  the other child was q_i
- height is given as max of height(p_k) and {(k-i) + height(q_i) for i = 1...k-1}

Implementation can be done as follows:
- the function returns for some node x a tuple (h, d, ans) where h is the height of
  the tree and d is the parent to child distance to node R (if present in subtree of x)
  and ans is max height of tree wrt R
  otherwise it is -1
- logic is:
  - let (h1, d1, ans1) = f(x.left)
  - let (h2, d2, ans2) = f(x.right)
  - h = 1 + max(h1, h2)
    d = if x == R then 0 otherwise: (d1 != d2 ? max(d1, d2) : -1)
    ans = max(ans1, ans2)
    if d1 != -1:
      ans = max(ans, d1 + h2 + 1)
    if d2 != -1:
      ans = max(ans, d2 + h1 + 1)
