## 一. 单调栈

### 单调栈的用途
- 单调栈是一种特殊的栈结构，栈中的元素保持单调递增或单调递减的顺序。它的主要用途是**寻找下一个更大/更小的元素**。
- 对于数组中的每个元素，找到其**右边或左边第一个比它大或小的元素**。

### 单调栈的两种写法
- 单调栈的实现有两种常见方式，区别在于对栈中元素的处理逻辑。
- **维护栈中元素**; **淘汰栈中不符合条件的元素**。

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
