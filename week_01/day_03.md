## 一. 一维前缀和
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

## 二. 二维前缀和
- 二维前缀和是前缀和在二维数组上的扩展。
- 定义前缀和数组 `prefix`，其中 `prefix[i][j]` 表示原数组 `matrix` 中从 `(0, 0)` 到 `(i, j)` 的子矩阵的和。
- 通过前缀和数组，任意子矩阵 `(x1, y1)` 到 `(x2, y2)` 的和可以通过 `prefix[x2 + 1][y2 + 1] - prefix[x1][y2 + 1] - prefix[x2 + 1][y1] + prefix[x1][y1]` 快速计算。
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

## 三. 一维差分差分是一种用于快速处理区间更新的技术。
- 差分是一种用于快速处理区间更新的技术。
- 定义差分数组 `diff`，其中 `diff[i] = arr[i] - arr[i-1]`。
- 通过对差分数组进行区间更新，可以快速更新原数组的区间值。
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

## 四. 二维差分
- 二维差分是差分在二维数组上的扩展。
- 通过对差分数组进行子矩阵更新，可以快速更新原数组的子矩阵值
- 多次对二维数组的某个子矩阵进行增减操作。
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

## 五. 整数二分

- **红蓝染色法**是一种直观的二分查找思想，通过将问题抽象为两种性质（红色和蓝色），将数组或区间分为两部分，从而快速定位目标值或满足条件的边界。
  - 红色：满足红色条件的元素。
  - 蓝色：满足蓝色条件的元素。
  - 通过二分查找，找到红色和蓝色的分界点。
- 二分答案：在答案的可能范围内进行二分查找，找到满足条件的最小值或最大值。
- 二分区间：在数组或区间中进行二分查找，找到满足条件的边界。


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

## 六. 浮点数二分
- **搜索范围**：在实数范围内定义一个区间 `[left, right]`，确保目标值位于该区间内。

- **中点判断**：
  - 计算中点 `mid = (left + right) / 2`。
  - 根据中点是否满足条件，缩小搜索范围：
  - 如果 `check(mid)` 为真，则目标值在左半部分，更新 `right = mid`。
  - 如果 `check(mid)` 为假，则目标值在右半部分，更新 `left = mid`。

- **终止条件**：
  - 精度控制：当区间长度小于某个极小值 `eps` 时，停止搜索。
  - 迭代次数：通过固定次数的迭代，确保结果达到所需精度。

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
