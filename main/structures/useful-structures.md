## Data structures

Data structures can be understood as black boxes which
allow a certain set of operations on data. The internal
design or *structure* of this black box determines how
fast or efficient these operations will be.

We now go through some basic data structures. We will
define these in two parts: supported operations (and time
complexities) and implementation hints.

It is common for data structures to use other data structures
as building blocks.

### Array / Random access list

#### Operations

- Append element: $O(1)$
- Delete element from end: $O(1)$
- Access element any some index $i$: $O(1)$

Arrays are bad at:
- Insertions/deletions at random positions.

#### Implementation

An array, internally, is a contiguous block of memory (simplified for understanding).
Since we know the address of start of array and size of each element,
we can get to any element fast by adding an offset to start.

Arrays are the most basic structures provided by almost all programming
languages. Usually a wrapper is provided which abstracts the low level aspects such
as indexing and memory allocation,  for example: C++ `vector<T>`.

### Linked list

Linked list allows traversal through *links*. Each element points to the
next element. This has certain advantages (and downsides too).
Each element in a linked list is usually called a *node*.

#### Operations

- Insert element after a node (given reference) : $O(1)$
- Delete node (given reference): $O(1)$

Linked lists are bad at:
- Random access

#### Implementation

A forward linked list allows iteration only in one direction.
Each node stores a *reference* to the next node
in sequence. In a doubly-linked list, each node stores a reference
to next and previous node.

> A reference can be simply understood as location or address of something.
> Given a reference, one can instantly access the underlying entity. These
> are also called *pointers* in C language family.

### Stack

#### Operations

- Push an element to top: $O(1)$
- Get value of topmost element: $O(1)$
- Remove element from top: $O(1)$

#### Implementation

Stacks can be implemented using arrays or linked lists.

### Queue

The simple 
