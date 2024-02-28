## DAG check

- See [abc285_d](https://atcoder.jp/contests/abc285/tasks/abc285_d)


## Bipartite check

Sample implementation:
```cpp
#define INF 1000000000
vi color;
vector<vi> adj;
int N, M;

bool dfs_bpcheck(int i) {
  for (int j = 0; j < sz(adj[i]); j++) {
    int v = adj[i][j];
    if (color[v] == INF) {
      color[v] = 1 - color[i];
      bool tmp = dfs_bpcheck(v);
      if (!tmp) return false;
    }
    else if (color[v] == color[i]) return false;
  }
  return true;
}
```