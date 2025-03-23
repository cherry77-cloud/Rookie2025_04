## 快速排序

- 快速排序是一种高效的排序算法，基于**分治法**的思想，平均时间复杂度为 `O(nlog n)`
- 快速排序的核心思想是通过一个基准值`pivot`将数组分为两部分：
  - 左部分：所有元素小于等于基准值。
  - 右部分：所有元素大于等于基准值。
- 然后递归地对左右两部分进行排序，最终完成整个数组的排序。
  - `Hoare` 分区法: 使用双指针从数组两端向中间扫描，交换不满足条件的元素。
  - `Lomuto` 分区法: 使用单指针从左到右扫描，将小于基准值的元素交换到左侧。

```c++
void quickSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;  // 递归终止条件
    
    int pivot_index = (left + right) >> 1;  // 选择中间元素作为基准点
    int pivot_value = arr[pivot_index];
    int i = left - 1, j = right + 1;  // 双指针
    
    while (i < j) {
        while (arr[++i] < pivot_value);   // 左指针向右移动
        while (arr[--j] > pivot_value);   // 右指针向左移动
        if (i < j) swap(arr[i], arr[j]);  // 交换不满足条件的元素
    }
    
    quickSort(arr, left, j);      // 递归处理左区间
    quickSort(arr, j + 1, right); // 递归处理右区间
}
```

---

## 快速选择算法
- 快速选择算法是一种用于查找数组中第 `k` 小或第 `k` 大元素的高效算法。它基于快速排序的分区思想，平均时间复杂度为 `O(n)`
- 快速选择算法的核心思想是通过分区（`Partition`）将数组分为两部分：
  - 左部分：所有元素小于等于基准值。
  - 右部分：所有元素大于等于基准值。
- 根据分区后基准值的位置，递归地在左部分或右部分中查找第 `k` 小的元素。

```c++
int findKthElement(vector<int>& nums, int left, int right, int k) {
    if (left == right) return nums[left];  // 递归终止条件
    
    int pivot_index = (left + right) >> 1;  // 选择中间元素作为基准点
    int pivot_value = nums[pivot_index];
    int i = left - 1, j = right + 1;  // 双指针
    
    while (i < j) {
        while (nums[++i] < pivot_value);    // 左指针向右移动
        while (nums[--j] > pivot_value);    // 右指针向左移动
        if (i < j) swap(nums[i], nums[j]);  // 交换不满足条件的元素
    }
    
    int left_partition_size = j - left + 1;  // 左分区的元素个数
    if (left_partition_size >= k) {
        return findKthElement(nums, left, j, k);  // 在左分区中查找
    }
    return findKthElement(nums, j + 1, right, k - left_partition_size);  // 在右分区中查找
}
```

---

## 归并排序

- 归并排序是一种基于分治法的高效排序算法，时间复杂度为 `O(nlog n)`
- 归并排序的核心思想是将数组分成两半，递归地对每部分排序，然后将有序的子数组合并成一个整体有序的数组。具体步骤：
  - 分割：将数组分成左右两部分。
  - 递归排序：对左右两部分分别递归排序。
  - 合并：将两个有序数组合并为一个有序数组。

```c++
void mergeSort(vector<int>& arr, int left, int right) {
    if (left >= right) return;  // 递归终止条件
    
    int mid = left + (right - left) / 2;
    mergeSort(arr, left, mid);           // 递归排序左半部分
    mergeSort(arr, mid + 1, right);      // 递归排序右半部分
    
    // 合并临时数组
    vector<int> tmp(right - left + 1);
    int i = left, j = mid + 1, k = 0;
    
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            tmp[k++] = arr[i++];
        } else {
            tmp[k++] = arr[j++];
        }
    }
    
    // 处理剩余元素
    while (i <= mid) tmp[k++] = arr[i++];
    while (j <= right) tmp[k++] = arr[j++];
    
    // 将临时数组拷贝回原数组
    for (int p = 0; p < k; p++) {
        arr[left + p] = tmp[p];
    }
}
```

---

## 逆序对统计
- 逆序对统计是一种基于归并排序的算法，用于计算数组中逆序对的数量。逆序对的定义是：如果 `i < j` 且 `nums[i] > nums[j]`，则 `(i, j)` 是一个逆序对。
- 逆序对统计的核心思想是利用归并排序的分治策略，在合并两个有序子数组时统计逆序对的数量。具体步骤：
  - 分割：将数组分成左右两部分。
  - 递归统计：递归统计左右两部分中的逆序对。
  - 合并统计：在合并两个有序子数组时，统计跨越左右两部分的逆序对。

```c++
long long countInversionPairs(vector<int>& nums, int left, int right) {
    if (left >= right) return 0;  // 递归终止条件
    
    int mid = left + (right - left) / 2;  // 计算中间点
    long long result = countInversionPairs(nums, left, mid) + 
                       countInversionPairs(nums, mid + 1, right);  // 递归计算左右子数组的逆序对
    
    // 合并临时数组
    vector<int> tmp(right - left + 1);
    int i = left, j = mid + 1, k = 0;
    
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            tmp[k++] = nums[i++];
        } else {
            tmp[k++] = nums[j++];
            result += mid - i + 1;  // 统计逆序对
        }
    }
    
    // 处理剩余元素
    while (i <= mid) tmp[k++] = nums[i++];
    while (j <= right) tmp[k++] = nums[j++];
    
    // 将临时数组拷贝回原数组
    for (int p = 0; p < k; p++) {
        nums[left + p] = tmp[p];
    }
    
    return result;
}
```

---

## 离散化
离散化是一种将大范围的数据映射到小范围的技术，常用于处理稀疏数据或优化空间复杂度

```c++
// 排序并去重
sort(all.begin(), all.end());
all.erase(unique(all.begin(), all.end()), all.end());

// 离散化函数: 找到 x 在 all 中的位置
int find(const vector<int>& all, int x) {
    return lower_bound(all.begin(), all.end(), x) - all.begin();
}

// 映射函数: 将 x 映射为从 0 开始的整数
int mapping(int x, unordered_map<int, int>& H, int& cnt) {
    if (H.count(x)) return H[x];  // 如果 x 已经存在，返回映射值
    return H[x] = cnt++;          // 否则，分配一个新的映射值
}
```
