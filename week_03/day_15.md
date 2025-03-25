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
        
        // 松弛操作
        for (int v = 0; v < n; ++v) {
            if (dist[u] + g[u][v] < dist[v]) {
                dist[v] = dist[u] + g[u][v];
            }
        }
    }
    
    return dist.back();
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

        // 松弛操作
        for (auto [v, w] : graph[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                pq.emplace(dist[v], v);
            }
        }
    }

    return dist.back();
}
```

---

## 二. `Bellman-Ford`算法

```cpp
int bellmanFord(const vector<Edge>& edges, int n) {
    vector<int> dist(n, INF);
    dist[0] = 0;
    // 松弛 n-1 轮
    for (int k = 0; k < n - 1; ++k) {
        bool updated = false;
        for (const auto& e : edges) {
            if (dist[e.from] != INF && dist[e.to] > dist[e.from] + e.weight) {
                dist[e.to] = dist[e.from] + e.weight;
                updated = true;
            }
        }
        if (!updated) break;
    }
    return dist.back();
}

// 有边数限制的最短路，最多经过 k 条边的最短路径
int BellmanFord(const vector<Edge>& edges, int n, int k) {
    vector<int> dist(n, INF);
    dist[0] = 0;
    for (int i = 0; i < k; ++i) {
        vector<int> backup = dist;  // 防止串联更新
        bool updated = false;
        for (const auto& e : edges) {
            if (backup[e.from] != INF && dist[e.to] > backup[e.from] + e.weight) {
                dist[e.to] = backup[e.from] + e.weight;
                updated = true;
            }
        }
        if (!updated) break;
    }
    return dist.back();
}
```

---
