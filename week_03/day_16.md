## 一. `Prim`算法

### 核心思想
- `Prim`算法用于求解加权无向图的最小生成树`MST`，其核心思想是：从任意起点出发，逐步扩展树结构，每次选择连接树与非树节点的最小权值边，直到覆盖所有顶点。
- 贪心策略：每次选择当前最优边（权值最小）
- 适用条件：无向连通图，边权可正可负（但必须有连通解）

### 算法步骤
- **初始化**：
  - 任选一个顶点作为起始点，加入最小生成树集合 `MST`
  - 维护一个优先队列（最小堆），存储连接 `MST` 与非 `MST` 顶点的边
- **迭代扩展**：
  - 从堆顶取出权值最小的边 `(u, v)`，其中 `u ∈ MST，v ∉ MST`
  - 将 `v` 加入 `MST`，并将 `v` 的所有邻接边加入堆
- **终止条件**：
  - 当 `MST` 包含所有顶点时结束

---

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

`Kruskal`算法是一种基于贪心思想的最小生成树（`MST`）算法，其核心是通过全局排序所有边，逐步选择不会构成环的最小权值边来构建生成树。算法通过**并查集**高效管理顶点连通性，确保每次选择的边连接两个原本不连通的子树。

- **边贪心策略**：将边按权值排序，从小到大逐个尝试加入生成树
- **并查集判环**：用并查集检查边的两个顶点是否已连通，避免成环

---

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

染色法判断二分图的核心在于利用图的二色着色性质。二分图是指顶点可以划分为两个不相交的集合，使得每条边的两个端点分别属于这两个集合。算法通过系统性地为顶点着色来验证这一性质：
- **双色交替规则**：选定两种颜色（通常记为0和1），要求相邻顶点必须染不同颜色。
- **冲突检测机制**：如果在着色过程中发现某条边的两个端点颜色相同，则立即判定该图不是二分图。

---

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

## 四. 匈牙利算法 - 二分图的最大匹配

匈牙利算法是求解二分图最大匹配问题的经典算法，其核心是通过**交替路径**的寻找与增广，逐步扩大匹配集合。算法体现**贪心+回溯**的特性：

**交替路径定义**
- 由未匹配边和已匹配边交替组成的路径
- 起点和终点都是未匹配顶点

**增广操作**
- 找到一条交替路径后，将该路径上的所有边状态取反（匹配↔非匹配）
- 每次增广操作可使匹配数+1

---

```cpp
int hungarian(const vector<vector<int>>& graph, int n_left, int n_right) {
    vector<int> right_match(n_right, -1);
    int result = 0;

    for (int u = 0; u < n_left; ++u) {
        vector<bool> vis(n_right, false);

        function<bool(int)> dfs = [&](int u) {
            for (int v : graph[u]) {
                if (!vis[v]) {
                    vis[v] = true;
                    if (right_match[v] == -1 || dfs(right_match[v])) {
                        right_match[v] = u;
                        return true;
                    }
                }
            }
            return false;
        };

        if (dfs(u)) ++result;
    }
    return result;
}
```

---
