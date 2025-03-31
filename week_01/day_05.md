<div align="center">
  <h1>滑动窗口</h1>
</div>


- 滑动窗口用于解决**子串/子数组的最优解问题（如最长无重复子串、最小覆盖子串等）**，通过动态调整窗口的左右边界，在 `O(n)` 时间内高效求解
- **右边界扩展（探索新字符）**，逐步扩大窗口范围，探索右侧新字符，更新窗口内数据状态
- **左边界收缩（保证窗口合法性）**，当窗口违反条件时，收缩左边界，通过移动left排除非法元素，直到窗口重新合法
- **答案更新时机（记录最优解）**

---

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

<div align="center">
  <h1>相向双指针</h1>
</div>


---
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int ans = 0, left = 0, right = height.size() - 1;
        while (left < right) {
            int area = min(height[left], height[right]) * (right - left);
            ans = max(ans, area);
            if (height[left] < height[right]) {
                left += 1;
            } else {
                right -= 1;
            }
        }
        return ans;
    }
};
```
