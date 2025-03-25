## 一. 区间合并

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
