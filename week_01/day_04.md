## 一. 指数型 + 排列型 + 组合型回溯

### 核心思想
回溯算法通过系统性地枚举所有可能性来解决问题，本质是`DFS`+状态回退的暴力搜索策略，适用于：
- 组合问题（无顺序要求），排列问题（有顺序要求），子集问题（所有可能组合）
- 🔢 指数型枚举（子集问题），每个元素选/不选
- 🔄 排列型枚举，考虑顺序，O(n!)
- 🎯 组合型枚举，固定长度子集

---

### 递归实现指数型枚举

```cpp
function<void(int)> dfs = [&](int u) {
    if (u == n) {
        subset.push_back(path);
        return;
    }
    dfs(u + 1);
    path.push_back(u + 1);
    dfs(u + 1);
    path.pop_back();
};


function<void(int, int)> dfs = [&](int u, int mask) {
    if (u == n) {
        vector<int> path;
        for (int i = 0; i < n; ++i) {
            if (mask & (1 << i)) {
                path.push_back(i + 1);
            }
        }
        subset.push_back(path);
        return;
    }
    dfs(u + 1, mask);
    dfs(u + 1, mask | (1 << u));
};
```

---

### 递归实现排列型枚举
```cpp
function<void(int)> dfs = [&](int u) {
    if (u == n) {
        result.push_back(path);
        return;
    }
    for (int i = 0; i < n; ++i) {
        if (!visited[i]) {
            visited[i] = true;
            path.push_back(i + 1);
            dfs(u + 1);
            path.pop_back();
            visited[i] = false;
        }
    }
};


function<void(int, int)> dfs = [&](int u, int mask) -> void {
    if (u == n) {
        result.push_back(path);
        return;
    }
    for (int i = 0; i < n; i++) {
        if (!(mask & (1 << i))) {
            path.push_back(i + 1);
            dfs(u + 1, mask | (1 << i));
            path.pop_back();
        }
    }
};
```

---

### 递归实现组合型枚举

```cpp
function<void(int, int)> dfs = [&](int u, int start) {
    if (u == m) {
        result.push_back(path);
        return;
    }
    for (int i = start; i < n; ++i) {
        path.push_back(i + 1);
        dfs(u + 1, i + 1);
        path.pop_back();
    }
};

function<void(int, int, int)> dfs = [&](int u, int cnt, int mask) {
    if (cnt + n - u < m)  return;
    if (cnt == m) {
        vector<int> path;
        for (int i = 0; i < n; ++i) {
            if (mask & (1 << i)) {
                path.push_back(i + 1);
            }
        }
        result.push_back(path);
        return;
    }
    if (u == n) return;
    dfs(u + 1, cnt + 1, mask | (1 << u));
    dfs(u + 1, cnt, mask);
};
```

---

## 二. 记忆化搜索
- 记忆化搜索是一种优化递归算法的经典技术，其核心思想是将**函数参数作为键，返回值作为值进行缓存**，从而避免重复计算。
- 记忆化搜索的本质是对计算图的缓存，将函数视为一个**有向无环图(DAG)**

  - 节点：函数的参数组合（即状态）
  - 边：状态转移关系
  - 缓存：存储已计算节点的结果

---

```cpp
// 矩阵中的最长递增路径
class Solution {
public:
    static constexpr int dirs[4][2] = {{-1, 0}, {0, -1}, {0, 1}, {1, 0}}; 
    
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        const int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> memo(m, vector<int>(n, -1));
        int ans = 0;
        
        function<int(int, int)> dfs = [&](int i, int j) {
            if (memo[i][j] != -1) return memo[i][j];
            int max_path = 1;
            for (const auto& dir : dirs) {
                int dx = i + dir[0], dy = j + dir[1];
                if (dx >= 0 && dx < m && dy >= 0 && dy < n && matrix[dx][dy] < matrix[i][j]) {
                    max_path = max(max_path, 1 + dfs(dx, dy));
                }
            }
            return memo[i][j] = max_path;
        };
        
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                ans = max(ans, dfs(i, j));
            }
        }
        return ans;
    }
};
```

---

```cpp
// 0-1背包 - 物品只能选0或1次
unordered_map<pair<int, int>, int> memo; // 记忆化存储
function<int(int, int)> dfs = [&](int i, int c) -> int {
    if (i < 0 || c <= 0) return 0; // 终止条件
    
    auto key = make_pair(i, c);
    if (memo.count(key)) return memo[key]; // 查缓存

    // 不选当前物品
    int not_take = dfs(i - 1, c);
    
    // 选当前物品（前提：容量足够）
    int take = (c >= w[i]) ? v[i] + dfs(i - 1, c - w[i]) : 0;
    
    return memo[key] = max(take, not_take); // 存缓存
};


// 完全背包（物品可选无限次）
unordered_map<pair<int, int>, int> memo; // 记忆化存储
function<int(int, int)> dfs = [&](int i, int c) -> int {
    if (i < 0 || c <= 0) return 0; // 终止条件
    
    auto key = make_pair(i, c);
    if (memo.count(key)) return memo[key]; // 查缓存

    // 不选当前物品
    int not_take = dfs(i - 1, c);
    
    // 选当前物品（可重复选，仍传i而非i-1）
    int take = (c >= w[i]) ? v[i] + dfs(i, c - w[i]) : 0;
    
    return memo[key] = max(take, not_take); // 存缓存
};
```

---
