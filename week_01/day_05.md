## `0-1`背包

```c++
// 经典二维DP版本
int zero_one_knapsack_naive(const vector<int>& volumes, const vector<int>& weights, int capacity) 
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
int zero_one_knapsack_optimized1(const vector<int>& volumes, const vector<int>& weights, int capacity) 
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
int zero_one_knapsack_optimized2(const vector<int>& volumes, const vector<int>& weights, int capacity) 
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
int complete_knapsack_naive(const vector<int>& volumes, const vector<int>& weights, int capacity) 
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
