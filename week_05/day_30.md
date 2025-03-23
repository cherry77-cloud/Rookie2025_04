## 最长上升子序列

```c++
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
