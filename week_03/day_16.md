## 一. `Prim`算法

```cpp
int prim(const vector<vector<int>>& g) {
    int n = g.size();
    vector<int> dist(n, INF);
    vector<bool> vis(n, false);
    priority_queue<pair<int, int>> pq;

    dist[0] = 0;
    pq.push({0, 0});

    int total_weight = 0;
    int nodes_in_mst = 0;

    while (!pq.empty()) {
        int u = pq.top().second;
        int d = -pq.top().first;
        pq.pop();

        if (vis[u]) continue;
        vis[u] = true;
        total_weight += d;
        nodes_in_mst++;

        for (int v = 0; v < n; ++v) {
            if (g[u][v] != 0 && !vis[v] && g[u][v] < dist[v]) {
                dist[v] = g[u][v];
                pq.push({-dist[v], v});
            }
        }
    }

    return (nodes_in_mst == n) ? total_weight : INF;
}
```

---
