# Minimum spanning tree

## Prim's algorithm

TODO

Sample implementation in C++:
```cpp
int N, M, num_vertices, cost;
vector< vii > Adj; // list of (vertex, weight)
priority_queue< ii, vii, greater<ii> > Q;
vi taken;

/* auxilliary method for PrimsMST */
void process (int u) {
  num_vertices++;
  taken[u] = 1;
  for (int i = 0; i < Adj[u].size(); i++) {
    ii v = Adj[u][i];
    if (taken[v.first] == 0) Q.push(ii(v.second, v.first));
  }
}

void PrimsMST () {
  cost = num_vertices = 0;
  taken.assign(N, 0);
  process(0);
  while (num_vertices < N) {
    ii x = Q.top(); Q.pop();
    if (taken[x.second]) continue;
    cost += x.first;
    process(x.second);
  }
}
```

## Kruskal's algorithm

TODO

Sample implementation in C++:
```cpp
int kruskalMST (int N, int M, vector<pair<int, ii>> EdgeList) {
  /* edges are in format : w(u, v), u, v */
  sort(EdgeList.begin(), EdgeList.end());
  UnionFind S (N);
  int cost = 0;

  for (int i = 0; i < M; i++) {
    a = EdgeList[i].second.first;
    b = EdgeList[i].second.second;
    w = EdgeList[i].first;

    if (S.sameSet(a, b)) continue; /* they form cycle */
    S.unionSet(a, b);
    cost += w;
  }

  return cost;
}
```

***