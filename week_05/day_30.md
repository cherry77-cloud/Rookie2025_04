## 一. 最长上升子序列

### 问题描述
- 给定一个整数序列 `nums`，找到其中最长的严格递增子序列的长度。
- **子序列的定义**：从原序列中删除一些元素（或不删除）后，剩余元素保持原有顺序形成的序列。

### 状态转移方程
- 设 `f[i]` 表示以 `nums[i]` 结尾的最长递增子序列的长度
- 对于每个元素 `nums[i]`，遍历它之前的所有元素 `nums[j]（j < i）`：
  
  - 如果 `nums[j] < nums[i]`，说明 `nums[i]` 可以接在 `nums[j]` 之后形成更长的递增子序列，`f[i] = max(f[i], f[j] + 1)`
  - 如果 `nums[j] >= nums[i]`，则无法扩展，保持 `f[i]` 不变
- 遍历所有 `f[i]`，取最大值即为最长递增子序列的长度。

### 贪心 + 二分查找

- 维护一个单调递增的数组 `tail`，`tail[i]` 表示长度为 `i+1` 的递增子序列的最小末尾值。
- 遍历每个元素，若其大于 `tail` 的最后一个值，则扩展 `tail`；否则用二分查找替换 `tail` 中第一个大于它的元素。
- 最终 `tail` 的长度即为 `LIS` 的长度。

---

```c++
// 递推
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> f(n, 1);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i])  f[i] = max(f[i], f[j] + 1);
        }
    }
    return ranges::max(f);
}

// 贪心 + 二分查找，返回最长上升子序列的长度和序列本身
pair<int, vector<int>> lengthAndLIS(vector<int>& nums) {
    vector<int> g; 
    vector<int> parent(nums.size(), -1);
    vector<int> index;

    for (int i = 0; i < nums.size(); ++i) {
        int x = nums[i];
        auto it = lower_bound(g.begin(), g.end(), x);
        int pos = it - g.begin();
        if (it == g.end()) {
            g.push_back(x);
            index.push_back(i);
        } else {
            *it = x;
            index[pos] = i;
        }
        if (pos > 0)  parent[i] = index[pos - 1];
    }

    vector<int> lis;
    if (!g.empty()) {
        int cur = index.back();
        while (cur != -1) {
            lis.push_back(nums[cur]);
            cur = parent[cur];
        }
        reverse(lis.begin(), lis.end());
    }
    return {g.size(), lis};
}
```

---

## 二. 最长公共子序列

### 问题描述

给定两个字符串 `text1` 和 `text2`，找出它们的最长公共子序列`LCS`的长度。公共子序列是指在两个字符串中都出现的字符序列，这些字符不需要连续，但必须保持原有的相对顺序。

### 状态转移方程
- 设 `dp[i][j]` 表示 `text1` 的前 `i` 个字符和 `text2` 的前 `j` 个字符的最长公共子序列的长度。

\begin{align*}
dp[i][j] = 
\begin{cases} 
dp[i-1][j-1]+1, & \text{if } text_1[i-1]=text_2[j-1] \\
\max(dp[i-1][j], dp[i][j-1]), & \text{if } text_1[i-1]\neq text_2[j-1]
\end{cases}
\end{align*}

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int n = text1.length(), m = text2.length();
    vector memo(n, vector<int>(m, -1));
    auto dfs = [&](this auto&& dfs, int i, int j) -> int {
        if (i < 0 || j < 0)  return 0;
        int &res = memo[i][j];
        if (res != -1)  return res;
        if (text1[i] == text2[j]) {
            return res = dfs(i - 1, j - 1) + 1;
        }
        return res = max(dfs(i - 1, j), dfs(i, j - 1));
    };
    return dfs(n - 1, m - 1);
}

int longestCommonSubsequence(string text1, string text2) {
    int n = text1.length(), m = text2.length();
    vector f(n + 1, vector<int>(m + 1, 0));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (text1[i] == text2[j]) {
                f[i + 1][j + 1] = f[i][j] + 1;
            } else {
                f[i + 1][j + 1] = max(f[i + 1][j], f[i][j + 1]);
            }
        }
    }
    return f[n][m];
}
```
