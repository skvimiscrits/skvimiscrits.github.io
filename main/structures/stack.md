# Stack

## Illustrations

### Chain substring removal

You are given two strings $S$ and $T = \text{abc}$.
Repeat this as many times as possible: If $S$ has $T$ as
a substring, remove that substring from $S$ (rest of the $S$
remains as it is)

Print final state of $S$.

**Ideas and solution**

First of all, will the final string be different if we change order
of removing substrings?

Since $\text{abc}$ never intersects with another instance of $\text{abc}$,
removing in any order will not impact final state.

We will choose this order of removing: find first instance of $\text{abc}$
starting from left and remove it.

To implement this, we will keep pushing characters to a stack. If top three
elements are $[\text{a}, \text{b}, \text{c}]$, we will remove all three.

At the end, the stack will contain the string in final state (from bottom to top).