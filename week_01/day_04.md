## 一. 指数型，排列型，组合型回溯

### 递归实现指数型枚举

```cpp
auto dfs = [&](int u, auto&& dfs) -> void {
    if (u == n) {
        subset.push_back(path);
        return;
    }
    
    // 不选
    dfs(u + 1, dfs);
    // 选
    path.push_back(u + 1);
    dfs(u + 1, dfs);
    path.pop_back();
};

auto dfs = [&](int u, int mask, auto&& dfs) -> void {
    if (u == n) {
        vector<int> path;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) {
                path.push_back(i + 1);
            }
        }
        subset.push_back(path);
        return;
    }
    dfs(u + 1, mask, dfs);               // 不选
    dfs(u + 1, mask | (1 << u), dfs);    // 选
};
```

---

