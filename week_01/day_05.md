## `0-1`背包

```c++
// 经典二维DP版本
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) 
{
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
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) 
{
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
int zero_one_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) 
{
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

## 完全背包

```c++
// 二维DP版本（基础实现）
int complete_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) 
{
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
int complete_knapsack(const vector<int>& volumes, const vector<int>& weights, int capacity) 
{
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

```c++
// 朴素三维DP版本 (O(N*C*K))
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity)
{
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
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity)
{
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
int multiple_knapsack(const vector<int>& volumes, const vector<int>& weights, const vector<int>& counts, int capacity)
{
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
