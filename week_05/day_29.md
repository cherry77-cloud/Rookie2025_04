## 一. `0-1`背包

### 问题描述
`0-1` 背包问题是一个经典的动态规划问题。问题的描述如下：
- 给定一个容量为 `capacity` 的背包和 `N` 个物品，每个物品有两个属性：
  
  - 体积：`volumes[i]`，表示第 `i` 个物品的体积。
  - 价值：`weights[i]`，表示第 `i` 个物品的价值。
- 目标是从这些物品中选择一些放入背包，使得：
  
  - 物品的总体积不超过背包的容量 `capacity`。
  - 物品的总价值最大。
    
- 注意：每个物品只能选择一次（要么放入背包，要么不放入），因此称为**0-1 背包**。

### 状态转移方程
- 设 `dp[i][j]` 表示前 `i` 个物品在容量为 `j` 的背包中能获得的最大价值。
- 对于每个物品 `i` 和容量 `j`，有两种选择：
- 不选择第 `i` 个物品：
  - 最大价值为前 `i-1` 个物品在容量 `j` 下的最大价值，即 `dp[i-1][j]`。
    
- 选择第 `i` 个物品：
  
  - 需要保证当前容量 `j` 大于等于物品 `i` 的体积 `volumes[i]`。
  - 最大价值为前 `i-1` 个物品在容量 `j-volumes[i]` 下的最大价值加上物品 `i` 的价值，即 `dp[i-1][j-volumes[i]] + weights[i]`。
---

```c++
// 经典二维DP版本
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) {
    int n = volumes.size();
    vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));
    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j <= capacity; ++j) {
            if (j >= volumes[i-1]) {
                dp[i][j] = max(dp[i-1][j], dp[i-1][j - volumes[i-1]] + weights[i-1]);
            } else {
                dp[i][j] = dp[i-1][j];
            }
        }
    }
    return dp[n][capacity];
}

// 滚动数组优化版本
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) {
    int n = volumes.size();
    vector<vector<int>> dp(2, vector<int>(capacity + 1, 0));
    for (int i = 1; i <= n; ++i) {
        int curr = i % 2;
        int prev = 1 - curr;
        for (int j = 0; j <= capacity; ++j) {
            if (j >= volumes[i-1]) {
                dp[curr][j] = max(dp[prev][j], dp[prev][j - volumes[i-1]] + weights[i-1]);
            } else {
                dp[curr][j] = dp[prev][j];
            }
        }
    }
    return dp[n % 2][capacity];
}

// 一维数组优化版本
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) {
    vector<int> dp(capacity + 1, 0);
    for (int i = 0; i < volumes.size(); ++i) {
        for (int j = capacity; j >= volumes[i]; --j) {
            dp[j] = max(dp[j], dp[j - volumes[i]] + weights[i]);
        }
    }
    return dp[capacity];
}
```

---

## 二. 完全背包

### 问题描述
完全背包问题与 `0-1` 背包问题类似，但有一个关键区别：**每个物品可以无限次选择（放入多个相同物品）**。问题的描述如下：

- 给定一个容量为 `capacity` 的背包和 `N` 个物品，每个物品有两个属性：
  
  - 体积：`volumes[i]`，表示第 `i` 个物品的体积。
  - 价值：`weights[i]`，表示第 `i` 个物品的价值。

- 目标是从这些物品中选择一些放入背包（允许重复选择），使得：
  
  - 物品的总体积不超过背包的容量 `capacity`。
  - 物品的总价值最大。

### 状态转移方程
- 设 `dp[i][j]` 表示前 `i` 个物品在容量为 `j` 的背包中能获得的最大价值。
- 对于每个物品 `i` 和容量 `j`，有两种选择：
- 不选择第 `i`个物品：
  
  - 最大价值为前 `i-1` 个物品在容量 `j` 下的最大价值，即 `dp[i-1][j]`。
- 选择第 `i` 个物品：
  
  - 需要保证当前容量 `j` 大于等于物品 `i` 的体积 `volumes[i]`。
  - 由于允许重复选择，状态转移应基于当前行的结果（即 `dp[i][j-volumes[i]]`，而非前一行）。最大价值为：`dp[i][j-volumes[i]] + weights[i]`。

---

```c++
// 二维DP版本（基础实现）
int complete_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) {
    int n = volumes.size();
    vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j <= capacity; ++j) {
            dp[i+1][j] = dp[i][j]; // 不选当前物品
            if (j >= volumes[i]) {
                // 允许重复选择，因此从 dp[i+1][j-volumes[i]] 转移
                dp[i+1][j] = max(dp[i+1][j], dp[i+1][j - volumes[i]] + weights[i]);
            }
        }
    }
    return dp[n][capacity];
}

// 一维DP版本（空间优化）
int complete_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) {
    vector<int> dp(capacity + 1, 0);
    for (int i = 0; i < volumes.size(); ++i) {
        // 正向遍历以支持重复选择
        for (int j = volumes[i]; j <= capacity; ++j) {
            dp[j] = max(dp[j], dp[j - volumes[i]] + weights[i]);
        }
    }
    return dp[capacity];
}
```

---

## 多重背包

### 问题描述
- 多重背包问题是 `0-1` 背包和完全背包的扩展，其核心区别在于，每个物品有选择次数上限。具体描述如下：
- 给定一个容量为 `capacity` 的背包和 `N` 个物品，每个物品有三个属性：
  
  - 体积：`volumes[i]`，表示第 `i` 个物品的体积。
  - 价值：`weights[i]`，表示第 `i` 个物品的价值。
  - 数量限制：`counts[i]`，表示第 `i` 个物品最多可被选择的次数。

- 目标是从这些物品中选择一些放入背包，使得：
  
  - 物品的总体积不超过背包的容量 `capacity`。
  - 物品的总价值最大。

### 与 0-1 背包和完全背包的对比
| 特性         | 0-1 背包                          | 完全背包                          | 多重背包                          |
|--------------|-----------------------------------|-----------------------------------|-----------------------------------|
| 物品选择     | 每个物品只能选一次                | 每个物品可以选无限次              | 每个物品最多选 `counts[i]` 次     |
| 状态转移方向 | 逆向遍历容量（防止重复选择）      | 正向遍历容量（允许重复选择）      | 遍历容量和选择次数                |
| 状态转移方程 | `dp[j] = max(dp[j], dp[j-w]+v)`   | `dp[j] = max(dp[j], dp[j-w]+v)`   | `dp[j] = max(dp[j], dp[j-k*w]+k*v)` |
| 核心差异     | 依赖上一行 `i-1` 的结果           | 依赖当前行 `i` 的结果             | 依赖上一行 `i-1` 的结果           |

---

```c++
// 朴素三维DP版本 (O(N*C*K))
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity) {
    int n = volumes.size();
    vector<vector<int>> dp(n + 1, vector<int>(capacity + 1, 0));

    for (int i = 1; i <= n; ++i) {
        for (int j = 0; j <= capacity; ++j) {
            dp[i][j] = dp[i - 1][j];
            int max_k = min(counts[i - 1], j / volumes[i - 1]);
            for (int k = 1; k <= max_k; ++k) {
                dp[i][j] = max(dp[i][j], dp[i - 1][j - k * volumes[i - 1]] + k * weights[i - 1]);
            }
        }
    }
    return dp[n][capacity];
}

// 二进制优化版本 (O(N*C*logK))
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity) {
    vector<pair<int, int>> items; // {体积, 价值}

    for (int i = 0; i < volumes.size(); ++i) {
        int k = 1;
        int remaining = counts[i];
        while (k <= remaining) {
            items.emplace_back(volumes[i] * k, weights[i] * k);
            remaining -= k;
            k *= 2;
        }
        if (remaining > 0) {
            items.emplace_back(volumes[i] * remaining, weights[i] * remaining);
        }
    }

    vector<int> dp(capacity + 1, 0);
    for (const auto& [v, w] : items) {
        for (int j = capacity; j >= v; --j) {
            dp[j] = max(dp[j], dp[j - v] + w);
        }
    }
    return dp[capacity];
}

// 单调队列优化版本 (O(N*C))
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity) {
    vector<int> dp(capacity + 1, 0);

    for (int i = 0; i < volumes.size(); ++i) {
        int v = volumes[i], w = weights[i], c = counts[i];
        
        // 对每个余数类单独处理
        for (int mod = 0; mod < v; ++mod) {
            deque<pair<int, int>> q; // 单调队列 {位置, 值}
            
            // 处理当前余数类的所有可能容量
            for (int k = 0; k * v + mod <= capacity; ++k) {
                int j = k * v + mod; // 实际容量
                int val = dp[j] - k * w; // 剔除k个物品的价值
                
                // 维护队列单调性
                while (!q.empty() && q.back().second <= val) q.pop_back();
                q.emplace_back(k, val);
                
                // 移除过期元素 (超过最大数量限制)
                while (q.front().first < k - c) q.pop_front();
                
                // 更新DP值
                dp[j] = max(dp[j], q.front().second + k * w);
            }
        }
    }
    return dp[capacity];
}
```
