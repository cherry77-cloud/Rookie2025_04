## 一维前缀和

```c++
vector<int> computePrefixSum(const vector<int>& nums) {
    int n = nums.size();
    vector<int> prefix(n + 1, 0);   // prefix[i] 表示原数组前 i 个元素的和
    for (int i = 1; i <= n; i++) {
        prefix[i] = prefix[i - 1] + nums[i - 1];
    }
    return prefix;
}

int rangeSum(const vector<int>& prefix, int l, int r) {
    return prefix[r + 1] - prefix[l];
}
```

---

## 二维前缀和
```c++
vector<vector<int>> computePrefixSum(const vector<vector<int>>& matrix) {
    int rows = matrix.size();
    int cols = matrix[0].size();

    vector<vector<int>> prefix(rows + 1, vector<int>(cols + 1, 0));

    for (int i = 1; i <= rows; i++) {
        for (int j = 1; j <= cols; j++) {
            prefix[i][j] = prefix[i - 1][j] + prefix[i][j - 1] - prefix[i - 1][j - 1] + matrix[i - 1][j - 1];
        }
    }

    return prefix;
}

int regionSum(const vector<vector<int>>& prefix, int x1, int y1, int x2, int y2) {
    return prefix[x2 + 1][y2 + 1] - prefix[x1][y2 + 1] - prefix[x2 + 1][y1] + prefix[x1][y1];
}
```

---

## 一维差分

```c++
// 构造差分数组, diff[i] 表示把下标 ≥i 的数都加上 diff[i]
vector<int> buildDiffArray(const vector<int>& arr) {
    int n = arr.size();
    vector<int> diff(n + 1, 0);
    diff[0] = arr[0];
    for (int i = 1; i < n; i++) {
        diff[i] = arr[i] - arr[i - 1];
    }
    return diff;
}

// 区间更新操作
void rangeUpdate(vector<int>& diff, int left, int right, int value) {
    diff[left] += value;
    diff[right + 1] -= value;
}

// 还原原数组
void restoreArray(vector<int>& diff) {
    for (int i = 1; i < diff.size(); i++) {
        diff[i] += diff[i - 1];
    }
}
```

---

