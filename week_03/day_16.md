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

## 二. `Kruskal`算法

```cpp
struct Edge {
    int u, v, w;
    bool operator<(const Edge& other) const {
        return w < other.w;
    }
};

int kruskal(vector<Edge>& edges, int n) {
    if (n <= 0) return INF;
    
    vector<int> parent(n);
    iota(parent.begin(), parent.end(), 0);
    
    auto find = [&](int x) {
        while (parent[x] != x) {
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    };

    sort(edges.begin(), edges.end());
    int total = 0, cnt = 0;

    for (const auto& [u, v, w] : edges) {
        int ru = find(u), rv = find(v);
        if (ru != rv) {
            parent[ru] = rv;
            total += w;
            if (++cnt == n - 1) break;
        }
    }

    return cnt == n - 1 ? total : INF;
}
```

---
