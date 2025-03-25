## 一. `Dijkstra`单源最短路

```cpp
int dijkstra(const vector<vector<int>>& g, int n) {
    vector<int> dist(n + 1, INF);
    vector<bool> visited(n + 1, false);
    
    dist[1] = 0;
    
    for (int i = 1; i < n; ++i) {
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
