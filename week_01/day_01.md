## 一. 快速排序
- 快速排序是一种高效的排序算法，基于**分治法**的思想
- 快速排序的核心思想是通过一个基准值`pivot`将数组分为两部分：
  - 左部分：所有元素小于等于基准值。
  - 右部分：所有元素大于等于基准值。
- 然后递归地对左右两部分进行排序，最终完成整个数组的排序。
  - `Hoare` 分区法: 使用双指针从数组两端向中间扫描，交换不满足条件的元素。
  - `Lomuto` 分区法: 使用单指针从左到右扫描，将小于基准值的元素交换到左侧。

```c++
void quick_sort(vector<int>& arr, int left, int right) {
    if (left >= right) return;  // 递归终止条件
    
    int pivot_index = (left + right) >> 1;  // 选择中间元素作为基准点
    int pivot_value = arr[pivot_index];
    int i = left - 1, j = right + 1;  // 双指针
    
    while (i < j) {
        while (arr[++i] < pivot_value);   // 左指针向右移动
        while (arr[--j] > pivot_value);   // 右指针向左移动
        if (i < j) swap(arr[i], arr[j]);  // 交换不满足条件的元素
    }
    
    quick_sort(arr, left, j);      // 递归处理左区间
    quick_sort(arr, j + 1, right); // 递归处理右区间
}
```

---

