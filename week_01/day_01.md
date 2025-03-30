<div align="center">
  <h1>快速排序</h1>
</div>

- 快速排序是一种高效的排序算法，基于**分治法**的思想，平均时间复杂度为 `O(nlogn)`
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

<div align="center">
  <h1>快速选择算法</h1>
</div>

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

<div align="center">
  <h1>归并排序</h1>
</div>

- 归并排序是一种基于**分治法**的高效排序算法，时间复杂度为 `O(nlogn)`
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

<div align="center">
  <h1>逆序对统计</h1>
</div>

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

<div align="center">
  <h1>排序链表</h1>
</div>

- 找到链表的中间结点 `head2` 的前一个节点，并断开 `head2` 与其前一个节点的连接。**把原链表均分成了两段更短的链表**。
- **分治**，递归调用 `sortList`，分别排序 `head`（只有前一半）和 `head2`。
- 排序后，得到了两个有序链表，合并两个有序链表，得到排序后的链表，返回链表头节点

---

```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        ListNode* pre = head;
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast && fast->next) {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        pre->next = nullptr;
        return slow;
    }

    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy;
        ListNode* cur = &dummy;
        while (l1 && l2) {
            if (l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        } 
        cur->next = l1 == nullptr ? l2 : l1;
        return dummy.next;
    }

    ListNode* sortList(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return head;
        }
        ListNode* head2 = middleNode(head);
        head = sortList(head);
        head2 = sortList(head2);
        return mergeTwoLists(head, head2);
    }
};
```

---

## 五. 离散化
### 核心概念
- 离散化是一种将大范围稀疏数据压缩到紧凑连续区间的算法技术
- 降低空间复杂度（将稀疏数据映射到密集索引）
- 保持数据相对顺序不变，为后续算法（如树状数组、线段树）提供预处理

---

```c++
// 1. 排序去重
sort(all.begin(), all.end());
all.erase(unique(all.begin(), all.end()), all.end());

// 2. 二分查找映射 - 保持相对顺序离散化
int find(const vector<int>& all, int x) {
    return lower_bound(all.begin(), all.end(), x) - all.begin();
}

// 3. 动态离散化 - 处理流式数据
unordered_map<int, int> mp;
int cnt = 0;
int dynamic_mapping(int x) {
    return mp.count(x) ? mp[x] : mp[x] = cnt++;
}
```
