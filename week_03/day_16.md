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

## 三. 染色法判断二分图

```cpp
bool isBipartite(const vector<vector<int>>& adj) {
    int n = adj.size();
    vector<int> color(n, 0);  //  0:未染色, 1:红色, 2:蓝色
    queue<int> q;

    for (int i = 0; i < n; ++i) {
        if (color[i] != 0) continue;
        
        q.push(i);
        color[i] = 1;
        
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            
            for (int v : adj[u]) {
                if (color[v] == 0) {
                    color[v] = 3 - color[u]; // 交替染色
                    q.push(v);
                } 
                else if (color[v] == color[u]) {
                    return false;
                }
            }
        }
    }
    return true;
}


bool isBipartite(const vector<vector<int>>& adj) {
    int n = adj.size();
    vector<int> color(n, 0); // 0:未染色, 1:红色, 2:蓝色
    
    function<bool(int, int)> dfs = [&](int u, int c) -> bool {
        color[u] = c;
        for (int v : adj[u]) {
            if (color[v] == 0) {
                if (!dfs(v, 3 - c)) return false;
            } 
            else if (color[v] == c) {
                return false;
            }
        }
        return true;
    };
    
    for (int i = 0; i < n; ++i) {
        if (color[i] == 0 && !dfs(i, 1)) {
            return false;
        }
    }
    return true;
}
```

---
