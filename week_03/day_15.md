## 一. `Dijkstra`单源最短路

```cpp
int dijkstra(const vector<vector<int>>& g) {
    int n = g.size();
    vector<int> dist(n, INF);
    vector<bool> visited(n, false);
    
    dist[0] = 0;
    
    for (int i = 0; i < n - 1; ++i) {
        int u = -1;
        for (int j = 0; j < n; ++j) {
            if (!visited[j] && (u == -1 || dist[j] < dist[u])) {
                u = j;
            }
        }
        
        if (u == -1) break;
        visited[u] = true;
        
        // // 松弛操作
        for (int v = 0; v < n; ++v) {
            if (dist[u] + g[u][v] < dist[v]) {
                dist[v] = dist[u] + g[u][v];
            }
        }
    }
    
    return dist[n-1] == INF ? -1 : dist[n-1];
}


// 堆优化
int dijkstra(const vector<vector<pair<int, int>>>& graph) {
    int n = graph.size();
    vector<int> dist(n, INF);
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;

    dist[0] = 0;
    pq.emplace(0, 0);

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

    return dist[n-1] == INF ? -1 : dist[n-1];
}
```

---
