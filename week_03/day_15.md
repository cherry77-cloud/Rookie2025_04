## 一. `Dijkstra`算法

`Dijkstra`算法是由荷兰计算机科学家`Edsger W. Dijkstra`于`1956`年提出的用于解决单源最短路径问题的经典算法。它可以找到图中从一个起始节点到所有其他节点的最短路径。`Dijkstra`算法要求图中不能有**负权边**。

### 核心思想
- `Dijkstra`算法采用贪心策略，逐步确定从源点到其他各顶点的最短路径。
- 维护一个已确定最短路径的顶点集合和一个未确定最短路径的顶点集合。
- 每次从未确定集合中选取距离源点最近的顶点，将其加入已确定集合。
- 然后对这个顶点的所有邻接点进行松弛操作，重复上述过程直到所有顶点都被确定。

---

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

`Bellman-Ford`算法是解决单源最短路径问题的另一种经典算法，由`Richard Bellman`和`Lester Ford`提出。与`Dijkstra`算法不同，`Bellman-Ford`可以处理图中存在**负权边**的情况，并且能够检测**负权回路**。

### 核心思想
- `Bellman-Ford`算法基于动态规划思想，通过松弛操作逐步逼近最短路径。
- 初始化所有顶点距离为无穷大，源点距离为0。
- 对图中所有边进行`V-1`轮松弛操作（`V`为顶点数）。
- 如果在`V-1`轮后还能继续松弛，说明图中存在负权回路。

---

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
`SPFA（Shortest Path Faster Algorithm）`是`Bellman-Ford`算法的一种优化实现。它通过队列优化减少了不必要的松弛操作，在大多数情况下能获得比`Bellman-Ford`更好的性能。

### 核心思想

- 使用队列维护待松弛的顶点集合
- 只有那些距离被更新的顶点才会被放入队列
- 避免了`Bellman-Ford`中对所有边的盲目松弛

---

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
`Floyd-Warshall`算法是一种解决所有顶点对最短路径问题的动态规划算法，由`Robert Floyd`和`Stephen Warshall`分别独立提出。它可以计算图中所有顶点之间的最短路径，并能处理负权边（但不能处理负权回路）。

### 核心思想
- `Floyd-Warshall`算法的核心思想是动态规划和逐步松弛。
- 使用三维动态规划思想，但通过巧妙地设计降为二维空间实现。
- 逐步考虑每个顶点作为中间节点的情况。
- 通过三重循环更新所有顶点对之间的最短距离。

---

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

## 五. `0-1 BFS`算法

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
