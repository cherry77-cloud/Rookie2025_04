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
