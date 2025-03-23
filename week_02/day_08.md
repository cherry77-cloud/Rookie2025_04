## 一. 单调栈

### 单调栈的核心思想
- 单调栈是一种特殊的栈结构，栈中的元素保持单调递增或单调递减的顺序。它的主要用途是**寻找下一个更大/更小的元素**。
- 对于数组中的每个元素，找到其**右边或左边第一个比它大或小的元素**。
- 单调栈的实现有两种常见方式，区别在于对栈中元素的处理逻辑。

---
```cpp
// 每个数左边第一个比它小的数，正向遍历，栈顶元素大于等于当前数组元素，淘汰
vector<int> leftFirstSmaller1(const vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, -1);
    stack<int> st;

    for (int i = 0; i < n; ++i) {
        while (!st.empty() && nums[st.top()] >= nums[i]) {
            st.pop();
        }
        if (!st.empty()) {
            res[i] = nums[st.top()];
        }
        st.push(i);
    }

    return res;
}

// 每个数左边第一个比它小的数，反向遍历，栈顶元素大于当前数组元素，直接给栈顶元素记录答案
vector<int> leftFirstSmaller2(const vector<int>& nums) {
    int n = nums.size();
    vector<int> res(n, -1);
    stack<int> st;

    for (int i = n - 1; i >= 0; --i) {
        while (!st.empty() && nums[st.top()] > nums[i]) {
            res[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }

    return res;
}
```

---

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    vector<int> left(n, 0);
    vector<int> right(n, n);
    stack<int> st;

    for (int i = 0; i < n; ++i) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            right[st.top()] = i;                // 当前柱子是栈顶柱子右边第一个小于它的柱子
            st.pop();
        }
        left[i] = st.empty() ? -1 : st.top();   // 栈顶是当前柱子左边第一个小于它的柱子
        st.push(i);
    }

    int ans = 0;
    for (int i = 0; i < n; ++i) {
        ans = max(ans, (right[i] - left[i] - 1) * heights[i]);
    }

    return ans;
}
```
---


## 二. 单调队列

### 单调队列的核心思想
- 单调队列是一种特殊的队列结构，队列中的元素保持单调递增或单调递减的顺序。它的主要用途是解决滑动窗口最值问题，优化动态规划问题。
- 单调队列的核心思想是通过维护队列的单调性，快速获取区间内的最值。具体来说：

  - **入队**：元素进入队尾，同时维护队列的单调性。如果新元素破坏了队列的单调性，则从队尾弹出元素，直到队列重新满足单调性。
  - **出队**：元素离开队首，通常是滑动窗口的左边界移动时。
  - **记录/维护答案**：根据队首元素记录当前窗口的最值。
---

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> result;
    deque<int> q;
    for (int i = 0; i < nums.size(); i++) {
        // 1. 入
        while (!q.empty() && nums[q.back()] <= nums[i]) {
            q.pop_back();  // 维护 q 的单调性
        }
        q.push_back(i);    // 入队

        // 2. 出
        if (i - q.front() >= k) {  // 队首已经离开窗口
            q.pop_front();
        }

        // 3. 记录答案
        if (i >= k - 1) {
            // 由于队首到队尾单调递减，所以窗口最大值就是队首
            result.push_back(nums[q.front()]);
        }
    }
    return result;
}
```

---

## 三. 哈希集合设计

哈希集合是一种基于哈希表的数据结构，用于存储不重复的元素。链地址法是解决哈希冲突的一种常见方法，通过在哈希表的每个槽中使用链表来存储具有相同哈希值的元素。

---
### 哈希函数
- 将元素映射到哈希表的一个槽中。
- 使用取模运算将元素的键值映射到固定范围的索引。

### 链地址法
- 每个槽中维护一个链表，用于存储具有相同哈希值的元素。
- 当发生哈希冲突时，将冲突的元素添加到对应槽的链表中。

### 操作
- **添加元素**：计算哈希值，将元素添加到对应槽的链表中（如果不存在）。
- **删除元素**：计算哈希值，从对应槽的链表中移除元素（如果存在）。
- **查找元素**：计算哈希值，在对应槽的链表中查找元素。

---

```c++
// 链地址法
class MyHashSet {
private:
    vector<list<int>> data;
    static const int base = 131;
    static int hash(int key) {
        int h = key % base;
        if (h < 0) h += base;
        return h;
    }

public:
    /** Initialize your data structure here. */
    MyHashSet() : data(base) {}
    
    void add(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return;
            }
        }
        data[h].push_back(key);
    }
    
    void remove(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                data[h].erase(it);
                return;
            }
        }
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return true;
            }
        }
        return false;
    }
};
```

---
