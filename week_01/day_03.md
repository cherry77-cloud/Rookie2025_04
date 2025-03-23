## 一维前缀和
- 前缀和是一种预处理技术，用于快速计算数组中任意区间的和。
- 定义前缀和数组 `prefix`，其中 `prefix[i]` 表示原数组 `nums` 的前 `i` 个元素的和。
- 通过前缀和数组，区间 `[l, r]` 的和可以通过 `prefix[r + 1] - prefix[l]` 快速计算。

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

## 二维差分

```c++
// 子矩阵更新操作
void regionUpdate(vector<vector<int>>& diff, int x1, int y1, int x2, int y2, int value) {
    diff[x1][y1] += value;          // 左上角加上 value
    diff[x1][y2 + 1] -= value;      // 右上角减去 value
    diff[x2 + 1][y1] -= value;      // 左下角减去 value
    diff[x2 + 1][y2 + 1] += value;  // 右下角加上 value
}

// 还原原数组
void restoreArray(vector<vector<int>>& diff, int n, int m) {
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < m; j++) {
            diff[i][j] += diff[i - 1][j];  // 从上到下累加
        }
    }
    for (int j = 1; j < m; j++) {
        for (int i = 0; i < n; i++) {
            diff[i][j] += diff[i][j - 1];  // 从左到右累加
        }
    }
}

// 初始化差分数组
vector<vector<int>> diff(n + 1, vector<int>(m + 1, 0));
for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        regionUpdate(diff, i, j, i, j, arr[i][j]);
    }
}
```

---

## 整数二分

```c++
// 查找第一个满足 is_blue 条件的元素的下标
int find_first_blue(int left, int right) {
    while (left < right) {
        int pivot = (left + right) / 2;
        if (is_blue(pivot)) {
            right = pivot;
        } else {
            left = pivot + 1;
        }
    }
    return left;
}

// 查找最后一个满足 is_red 条件的元素的下标
int find_last_red(int left, int right) {
    while (left < right) {
        int pivot = (left + right + 1) / 2;
        if (is_red(pivot)) {
            left = pivot;
        } else {
            right = pivot - 1;
        }
    }
    return left;
}
```

---

## 浮点数二分

```c++
double binary_search(double left, double right) {
    const double eps = 1e-7;
    while (right - left > eps) {
        double mid = (left + right) / 2;
        if (check(mid)) {
            right = mid;
        } else {
            left = mid;
        }
    }
    return left;
}

double binary_search(double left, double right) {
    const int iterations = 1000;
    for (int i = 0; i < iterations; i++) {
        double mid = (left + right) / 2;
        if (check(mid)) {
            right = mid;
        } else {
            left = mid;
        }
    }
    return left;
}
```
