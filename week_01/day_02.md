## 一. 归并排序

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

## 二. 逆序对统计
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
    vector<int> temp(right - left + 1);
    int i = left, j = mid + 1, k = 0;
    
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            temp[k++] = nums[i++];
        } else {
            temp[k++] = nums[j++];
            result += mid - i + 1;  // 统计逆序对
        }
    }
    
    // 处理剩余元素
    while (i <= mid) temp[k++] = nums[i++];
    while (j <= right) temp[k++] = nums[j++];
    
    // 将临时数组拷贝回原数组
    for (int p = 0; p < k; p++) {
        nums[left + p] = temp[p];
    }
    
    return result;
}
```
