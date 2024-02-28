## Unordered sets/maps

- $O(1)$ deletion/insertion/presence check
  can be assumed.
- No ordering (i.e. traversal might not provide
  sorted values)

## MEX

If there are $N$ elements (all non-negative) then mex of these
always in {0,1,...N} (pigeon-hole like principal)

- See: [this](https://atcoder.jp/contests/abc272/tasks/abc272_e)

How to find range MEX efficiently?

- Offline? Use MO’s algorithm with MEX Data structure
  - Complexity -> O((N+Q)*sqrt(N)*log(N))
- Offline? Use segment tree - min with point update
  - https://codeforces.com/contest/1436/submission/96756726


MEX Data structure
- (numbers in range [1, n], small enough n (<= 1e5)
- Go by definition - Smallest number absent in the set
- Maintain two sets P, A (present, absent)
- Initially, P={}, A={1, …, n}
- Adding or removing elements means swapping elements in P & A
- MEX(P) = smallest element in A

MEX Data structure (To find general MEX, i.e., MEX >= x) and large values
(say <= 10^9) . Efficiently compute queries of form on set of integers S

- Add x to S
- Remove x from S
- Return Mex(S)

Set approach: Store ranges
- Add x to S : either add new range or merge range
- Remove x : split range
- First range can provide Mex(S)