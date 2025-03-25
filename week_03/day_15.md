## 一. `Dijkstra`算法

```cpp
// 邻接矩阵存图
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


// 邻接表存图 + 堆优化
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
// 边集数组存图
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

## 三. `SPFA`算法

```cpp
int SPFA(const vector<vector<pair<int, int>>>& graph) {
    int n = graph.size();
    vector<int> dist(n, INF);
    vector<bool> in_queue(n, false);
    queue<int> q;

    dist[0] = 0;
    q.push(0);
    in_queue[0] = true;

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        in_queue[u] = false;

        for (const auto& [v, w] : graph[u]) {
            if (dist[v] > dist[u] + w) {
                dist[v] = dist[u] + w;
                if (!in_queue[v]) {
                    q.push(v);
                    in_queue[v] = true;
                }
            }
        }
    }

    return dist.back();
}
```

---

## 四. `Floyd-Warshall`算法

```cpp
vector<vector<int>> dist(n, vector<int>(n, INF));
for (int i = 0; i < n; ++i) dist[i][i] = 0;

for (int i = 0; i < m; ++i) {
    int x, y, z;
    cin >> x >> y >> z;
    dist[x][y] = min(dist[x][y], z);
}

void floydWarshall(vector<vector<int>>& dist) {
    int n = dist.size();
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (dist[i][k] != INF && dist[k][j] != INF) {
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```

---

## 五. 0-1 BFS

```cpp
class ZeroOneBFS {
    static constexpr int DIRS[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

public:
    static int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dist(m, vector<int>(n, INT_MAX));
        dist[0][0] = 0;
        deque<pair<int, int>> q;
        q.emplace_front(0, 0);

        while (!q.empty()) {
            auto [i, j] = q.front();
            q.pop_front();

            for (auto& [dx, dy] : DIRS) {
                int x = i + dx, y = j + dy;
                if (x >= 0 && x < m && y >= 0 && y < n) {
                    int cost = grid[x][y];
                    if (dist[i][j] + cost < dist[x][y]) { // 松弛操作
                        dist[x][y] = dist[i][j] + cost;
                        cost == 0 ? q.emplace_front(x, y) : q.emplace_back(x, y);
                    }
                }
            }
        }

        return dist[m-1][n-1];
    }
};
```
