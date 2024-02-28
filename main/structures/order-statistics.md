We discuss data structures supporting the following
operations (assume integers):
- Insert $x$
- Delete an occurence of $x$
- Find number of elements less than $x$ 
- Find element at position $k$

## Order statistics tree

Supports each operation in $O(\lg{n})$

### Inbuilt implementations

```cpp
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;
typedef tree<
  int,
  null_type,
  less<int>,
  rb_tree_tag,
  tree_order_statistics_node_update
> ordered_set;
// to use it as a multiset, replace `int` by `pair<int, int>`.

int main() {
  ordered_set ost;
  ost.insert(1);
  ost.insert(3);
  ost.insert(5);
  ost.insert(7);

  cout << ost.order_of_key(8) << '\n';
  // 4 elements strictly less than 8

  cout << ost.order_of_key(7) << '\n'; // 3

  cout << *ost.find_by_order(2) << '\n';
  // 1,3,5,7 -> 5 (by 0-indexing)
}
```
***

## Sparse segment tree

Works well with point updates and range queries.

Idea is to create nodes on only on update.

```cpp
struct Node {
  Node* cl;
  Node* cr;
  long val, l, r;
  Node(long _val, long _l, long _r): val(_val), l(_l), r(_r) {
    cl = NULL;
    cr = NULL;
  }
};


long query (Node* node, long l, long r) {
  if (l > node->r || r < node->l) return 0;
  if (l <= node->l && node->r <= r) return node->val;
  long ans = 0;
  if (node->cl) ans += query(node->cl, l, r);
  if (node->cr) ans += query(node->cr, l, r);
  return ans;
}

void update (Node* node, long i, long x) {
  node->val += x;
  if (node->l == node->r) {
    // it holds that node->l = node->r = i
    return;
  }
  // take care of floor in case of negative l, r
  long m = node->l + (node->r - node->l) / 2;
  // (l, m) and (m+1, r)
  if (i <= m) {
    if (node->cl == NULL)
      node->cl = new Node(0, node->l, m);
    update(node->cl, i, x);
  } else {
    if (node->cr == NULL)
      node->cr = new Node(0, m + 1, node->r);
    update(node->cr, i, x);
  }
}

int main() {
  Node* root = new Node(0, -1e15, 1e15);
  update(root, -1000, 2);
  update(root, -500, 3);
  cout << query(root, -1000, 0) << '\n'; // 5
}
```

***


## Application: Number of elements in range in dynamic set

Three types of operations (all integers):
- Insert element $x$
- Delete one occurrence of element $x$
- Find number of elements in range $[l, r]$

Constraints:
- $k \le 10^5$ operations
- $-10^9 \le x,l,r \le 10^9$

**Solution**

Insertions and deletions can be handled directly.

For counting elements in range, find the minimum and
maximum element which is present in the set and lies
in the given range.
Finding the positions leads to the answer.

***