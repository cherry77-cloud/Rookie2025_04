## 最长上升子序列

```c++
// 记忆化搜索
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> memo(n);
    auto dfs = [&](this auto&& dfs, int i) -> int {
        int &res = memo[i];
        if (res > 0)  return res;
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                res = max(res, dfs(j));
            }
        }
        return ++res;
    };
    int ans = 0;
    for (int i = 0; i < n; i++) {
        ans = max(ans, dfs(i));
    }
    return ans;
}

// 递推
int lengthOfLIS(vector<int>& nums) {
    int n = nums.size();
    vector<int> f(n);
    for (int i = 0; i < n; i++) {
        f[i] = 0;
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                f[i] = max(f[i], f[j]);
            }
        }
        f[i]++;
    }
    return ranges::max(f);
}

// 贪心 + 二分查找
int lengthOfLIS(vector<int>& nums) {
    vector<int> g;  // 定义 g[i] 表示长为 i+1 的上升子序列的末尾元素的最小值。
    for (int x : nums) {
        auto it = ranges::lower_bound(g, x);
        if (it == g.end()) {
            g.push_back(x);
        } else {
            *it = x;
        }
    }
    return g.size();
}

// 返回最长上升子序列的长度和序列本身
pair<int, vector<int>> lengthAndLIS(vector<int>& nums) {
    vector<int> g;  // g[i] 表示长为 i+1 的上升子序列的末尾元素的最小值
    vector<int> parent(nums.size(), -1);  // 记录每个元素的前驱节点
    vector<int> index;  // 记录 g 中每个元素在 nums 中的索引

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

        // 记录前驱节点
        if (pos > 0) {
            parent[i] = index[pos - 1];
        }
    }

    // 构造最长上升子序列
    vector<int> lis;
    if (!g.empty()) {
        int cur = index.back();  // 从最长子序列的最后一个元素开始回溯
        while (cur != -1) {
            lis.push_back(nums[cur]);
            cur = parent[cur];
        }
        reverse(lis.begin(), lis.end());  // 反转得到正确的顺序
    }

    // 返回长度和序列
    return {g.size(), lis};
}
```

---

