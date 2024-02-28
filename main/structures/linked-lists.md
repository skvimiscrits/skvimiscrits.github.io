## Reverse a linked list

Keep 3 pointers -> trailing, cur, nxt

Make fwd linkage from cur to trailing. Move to nxt

### Reverse linked list in groups of $k$

We can break it down into a method which reverses a part
of linked list given the start and end node (while keeping
the correct link with the node next to end of this part)
and returns the new head.

```cpp
ListNode* reversePart (ListNode* head, ListNode* tail) {
    ListNode* newHead = tail->next;
    tail->next = nullptr;
    while (head != nullptr) {
        ListNode* nxt = head->next;
        head->next = newHead;
        newHead = head;
        head = nxt;
    }
    return newHead;
}
```

We can reverse in groups of $k$ now:
```cpp
ListNode* reverseKGroup(ListNode* head, int k) {
    ListNode* base = new ListNode(0, head);
    ListNode* tmp = base;
    while (tmp != nullptr) {
        ListNode* tail = tmp;
        for (int i = 0; i < k && tail != nullptr; i++)
            tail = tail->next;
        ListNode* newTail = tmp->next;
        if (tail != nullptr)
          tmp->next = reversePart(tmp->next, tail);
        tmp = newTail;
    }
    return base->next;
}
```


## Merge sorted linked lists

### Two linked lists

Use swapping to avoid excessive conditions.

```cpp
ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
    ListNode* head = new ListNode();
    ListNode* tmp = head;
    while (list1 != nullptr || list2 != nullptr) {
        if (list1 == nullptr) swap(list1, list2);
        if (list2 != nullptr && list2->val < list1->val) swap(list1, list2);
        tmp->next = new ListNode(list1->val);
        list1 = list1->next;
        tmp = tmp->next;
    }
    return head->next;
}
```

### Large number of linked lists with small size

If $N$ lists are given but each one has small size (at most $K$) then,
we can use divide and conquer algorithm to merge these in $O(NK \lg N)$
as the problem subtree has $\lg N$ levels and each level will have combined
merging of $NK$ elements.

> Even if the distribution is not uniform, i.e., only total number of elements is known being $M$
> the time complexity will be $O(M \lg N)$ because on each level of the tree, $M$ elements are being traversed.

Sample divide-and-conquer based recursion:
```cpp
ListNode* mergeKLists(vector<ListNode*>& lists, int l, int r) {
    if (l > r) return nullptr;
    if (l == r) return lists[l];
    int mid = (l + r) / 2;
    return merge(mergeKLists(lists, l, mid), mergeKLists(lists, mid + 1, r));
}

ListNode* mergeKLists(vector<ListNode*>& lists) {
    return mergeKLists(lists, 0, lists.size() - 1);
}
```

## Remove nth node from end in a fwd linked list
  (overall length not given)

Trivial solution is going to end to find length and then go back again
and traverse to (size-n+1)th node

Single pass solution is to have a trailing pointer which trails n-1 nodes behind

Sample code:
```cpp
ListNode* tmp = head;
ListNode* trail = new ListNode(0, head); /* trailing pointer set just before head */
ListNode* ans = trail; /* for later */
int done = 0;
while (tmp->next != nullptr) {
    tmp = tmp->next;
    done++;
    if (done >= n) trail = trail->next; /* trail pointer starts incr. only when n elements traversed */
}
trail->next = trail->next->next; /* deleting next element and switching to the new next */
return ans->next; /* ans is set to just before head */
```

## Swapping two nodes in a fwd linked list

If you have node (pointers) A and B,
to swap these you need another node (pointer) X preceding A
such that you can change the linkages like:
```
A->next = B->next
B->next = A
X->next = B
```

## Right-Rotate by k places (length not given)
(trivialish)
- find length and tail
- make circular
- start from head and break linkage from new tail

## Remove all duplicates from sorted fwd list 

> Clean way to handle changing head of list:
> Maintain a node (whose value does not matter) and whose
> next element will be the start of the result list.

Note: if there are duplicates of x, remove all occurrences of x

Let X be the trailing pointer for head. Let T = X (as temp. pointer)
- check T->next and subsequent nodes until a different valued node
  (or end) occurs. Keep a count of traversed nodes
- If this count is greater than 1, create new linkage for T->next
- Advance T
- Output X->next

## Partition fwd list (all elements < x before rest)

Note: Relative order must be preserved

- Find tail
- Traverse list until end and move elements >= x to a new list
- Join the two lists

Need to take care of:
- New head of the list
- Case where all elements are moved to a new list
