## 一. 双序列双指针

### 核心思想
- 双序列双指针算法适用于两个有序序列的合并、查找或匹配问题。通过两个指针分别遍历两个序列，利用有序性减少不必要的比较，将时间复杂度优化至 `O(n + m)`
- 典型应用场景: 合并两个有序数组/链表（如归并排序的合并步骤）, 查找两个有序序列的共同元素（如求交集、并集）, 判断一个序列是否为另一个序列的子序列

---

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */

ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode* preHead = new ListNode(-1);
    auto prev = preHead;
    while (l1 != nullptr && l2 != nullptr) {
        if (l1->val < l2->val) {
            prev->next = l1;
            l1 = l1->next;
        } else {
            prev->next = l2;
            l2 = l2->next;
        }
        prev = prev->next;
    }
    prev->next = l1 == nullptr ? l2 : l1;
    return preHead->next;
}
```

---

## 二. 滑动窗口

```cpp
int lengthOfLongestSubstring(string s) {
    int n = s.length(), ans = 0, left = 0;
    unordered_map<char, int> cnt; // 维护从下标 left 到下标 right 的字符
    for (int right = 0; right < n; right++) {
        char c = s[right];
        cnt[c]++;
        while (cnt[c] > 1) { // 窗口内有重复字母
            cnt[s[left]]--; // 移除窗口左端点字母
            left++; // 缩小窗口
        }
        ans = max(ans, right - left + 1); // 更新窗口长度最大值
    }
    return ans;
}
```

---
