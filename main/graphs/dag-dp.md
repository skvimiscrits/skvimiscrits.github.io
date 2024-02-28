## Directed Acyclic Graphs and DP

If some recurrence relation $f$ is defined on each node
in the form:

$$
f_u = g(f_{v_1}, \ldots, f_{v_k})
$$

where $\{v_i\}$ are nodes adjacent to $u$, then
we can use concepts of dynamic programming like memoization.

## Example 1: Longest path

Here $g$ will be simply defined as
$1 + \max(f_{v_i})$