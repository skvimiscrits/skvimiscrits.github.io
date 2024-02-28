## Least Recently Used (LRU) set

### Requirements
- Insert an element (not already present)
- Delete an element
- Get the least recently used element.

> Here, by 'used' we mean inserted because the
> only other operation is deletion.

All of these operations should be $O(1)$.

### Solution

We require:
- Fast access, insertion and deletion: a hash table
  seems to be the suitable choice.
- Ordering: this hints to a sequence based structure

Combining a hash table and a linked list gives us
the required solution.
- The hash table links a key to a node in linked list.
- The linked list keeps the ordering allowing us to know
  the least recently used element.

Both structures should be kept consistent on insertions
and deletions.

## Frequency based set

### Requirements
- Insert an element (may be duplicate)
- Delete one instance of an element
- Get the element with least frequency.

All of these operations should be $O(1)$.

### Solution

> A more generalized version of this problem (inserting $k$ instances at once) can
> be solved using an ordered set by keeping pairs $(f, E)$ where $f$ is frequency
> of element $E$ (assuming $E$ is hash-able). But the updation time is
> $O(\lg{n})$.

We require:
- Fast access, insertion, deletion - hash table
- Ordering - for frequency

This can be achieved by using a linked list where
nodes are in order of increasing frequency. A hash table
is also required for random access to the linked list elements.

The linked list will be structured in the following way:
- Each node will store frequency and a set of elements.
- The set implementation should support fast insertion
  and deletion (hash-table based sets support this).
- The head of the list always gives the least frequent element.

***