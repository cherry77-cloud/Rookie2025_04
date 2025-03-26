## 一. 区间合并
区间合并算法用于将一组可能有重叠的区间合并成互不重叠的区间集合。其核心策略是：

- **排序预处理**：按区间起始点排序
- **线性扫描合并**：逐个检查是否与结果集中的最后一个区间重叠

---

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    ranges::sort(intervals);
    vector<vector<int>> ans;
    for (auto& p : intervals) {
        if (!ans.empty() && p[0] <= ans.back()[1]) {
            ans.back()[1] = max(ans.back()[1], p[1]);
        } else {
            ans.emplace_back(p);
        }
    }
    return ans;
}
```

---

## 二. 区间分组

- **排序预处理**：按区间起始点排序：将所有区间按左端点升序排列，确保处理顺序合理。
- **贪心分配组别**：动态维护组的最早结束时间，使用最小堆记录每组当前的最右端点（即组内最后一个区间的结束时间）。
- 若当前区间起始点 > 堆顶（最早结束时间）：可合并到该组 → 更新该组的最右端点（弹出旧值，压入当前区间的右端点）。
- 若当前区间起始点 ≤ 堆顶：必须新建一组 → 直接压入当前区间的右端点。
---

```cpp
int interval_group(vector<pair<int, int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    priority_queue<int, vector<int>, greater<int>> heap;
    for (const auto& interval : intervals) {
        if (!heap.empty() && heap.top() < interval.first) {
            heap.pop();
            heap.push(interval.second);
        } else {
            heap.push(interval.second);
        }
    }
    return heap.size();
}
```

---

## 三. 区间点覆盖问题 + 最大不相交区间问题

- **右端点排序**：所有区间按结束位置升序排列
- **贪心处理**
  - 点覆盖：每次选当前区间右端点，覆盖后续可能重叠的区间
  - 不相交区间：直接统计不重叠的区间数量

---

```cpp
int max_disjoint_intervals(vector<pair<int, int>>& intervals) {
    sort(intervals.begin(), intervals.end(), [](const auto& a, const auto& b) {
        return a.second < b.second;
    });
    int count = 0;
    int last_end = INT_MIN;
    for (const auto& interval : intervals) {
        if (interval.first > last_end) {
            last_end = interval.second;
            count++;
        }
    }
    return count;
}
```

---
