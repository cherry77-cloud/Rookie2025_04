## 一. 单调栈



```cpp
int largestRectangleArea(vector<int>& heights) 
{
    int n = heights.size();
    vector<int> left(n, 0);
    vector<int> right(n, n);
    stack<int> st;

    for (int i = 0; i < n; ++i) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            right[st.top()] = i;              // 当前柱子是栈顶柱子右边第一个小于它的柱子
            st.pop();
        }
        left[i] = st.empty() ? -1 : st.top(); // 栈顶是当前柱子左边第一个小于它的柱子
        st.push(i);
    }

    int ans = 0;
    for (int i = 0; i < n; ++i) {
        ans = max(ans, (right[i] - left[i] - 1) * heights[i]);
    }

    return ans;
}
```
