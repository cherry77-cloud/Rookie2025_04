## 一. 快速排序

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

## 二. 快速选择算法
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
