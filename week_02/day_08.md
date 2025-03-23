## 一. 单调栈

### 单调栈的用途
- 单调栈是一种特殊的栈结构，栈中的元素保持单调递增或单调递减的顺序。它的主要用途是**寻找下一个更大/更小的元素**。
- 对于数组中的每个元素，找到其**右边或左边第一个比它大或小的元素**。

### 单调栈的两种写法
- 单调栈的实现有两种常见方式，区别在于对栈中元素的处理逻辑。
- 维护栈中元素，栈中存储的是尚未找到下一个更大/更小元素的「候选项」。
- 淘汰栈中不符合条件的元素，栈中存储的是「候选边界」。

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

