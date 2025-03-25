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

## 三. 区间点覆盖问题 + 最大不相交区间问题

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
