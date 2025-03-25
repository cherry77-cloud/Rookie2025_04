## 一. `Dijkstra`单源最短路

```cpp
int dijkstra(const vector<vector<int>>& g, int n) {
    vector<int> dist(n + 1, INF);
    vector<bool> visited(n + 1, false);
    
    dist[1] = 0;
    
    for (int i = 1; i < n; ++i) {  // 节点编号从1开始
        int u = 0;
        for (int j = 1; j <= n; ++j) {
            if (!visited[j] && (u == 0 || dist[j] < dist[u])) {
                u = j;
            }
        }
        
        if (u == 0) break;
        visited[u] = true;
        
        for (int v = 1; v <= n; ++v) {
            if (dist[u] + g[u][v] < dist[v]) {
                dist[v] = dist[u] + g[u][v];
            }
        }
    }
    
    return dist[n] == INF ? -1 : dist[n];
}
```

---


## 二. `Dijkstra`单源最短路(堆优化)

```cpp
int dijkstra(const vector<vector<pair<int, int>>>& graph) {
    int n = graph.size() - 1;
    vector<int> dist(n + 1, INF);
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;

    dist[1] = 0;
    pq.emplace(0, 1);

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();
        if (d > dist[u]) continue;

        for (auto [v, w] : graph[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                pq.emplace(dist[v], v);
            }
        }
    }

    return dist[n] == INF ? -1 : dist[n];
}
```

---

