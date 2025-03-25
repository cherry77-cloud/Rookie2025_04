## 一. 指数型，排列型，组合型回溯

### 递归实现指数型枚举

```cpp
function<void(int)> dfs = [&](int u) {
    if (u == n) {
        subset.push_back(path);
        return;
    }
    
    dfs(u + 1);
    
    path.push_back(u + 1);
    dfs(u + 1);
    path.pop_back();
};


function<void(int, int)> dfs = [&](int u, int mask) {
    if (u == n) {
        vector<int> path;
        for (int i = 0; i < n; ++i) {
            if (mask & (1 << i)) {
                path.push_back(i + 1);
            }
        }
        subset.push_back(path);
        return;
    }
    
    dfs(u + 1, mask);
    dfs(u + 1, mask | (1 << u));
};
```

---

### 递归实现排列型枚举
```cpp
function<void(int)> dfs = [&](int u) {
    if (u == n) {
        result.push_back(path);
        return;
    }
    
    for (int i = 0; i < n; ++i) {
        if (!visited[i]) {
            visited[i] = true;
            path.push_back(i + 1);
            dfs(u + 1);
            path.pop_back();
            visited[i] = false;
        }
    }
};


function<void(int, int)> dfs = [&](int u, int mask) -> void {
    if (u == n) {
        result.push_back(path);
        return;
    }
    
    for (int i = 0; i < n; i++) {
        if (!(mask & (1 << i))) {
            path.push_back(i + 1);
            dfs(u + 1, mask | (1 << i));
            path.pop_back();
        }
    }
};
```

---

### 递归实现组合型枚举

```cpp
function<void(int, int)> dfs = [&](int u, int start) {
    if (u == m) {
        result.push_back(path);
        return;
    }
    
    for (int i = start; i < n; ++i) {
        path.push_back(i + 1);
        dfs(u + 1, i + 1);
        path.pop_back();
    }
};

function<void(int, int, int)> dfs = [&](int u, int cnt, int mask) {
    if (cnt + n - u < m)  return;
    
    if (cnt == m) {
        vector<int> path;
        for (int i = 0; i < n; ++i) {
            if (mask & (1 << i)) {
                path.push_back(i + 1);
            }
        }
        result.push_back(path);
        return;
    }
    if (u == n) return;
    
    dfs(u + 1, cnt + 1, mask | (1 << u));
    dfs(u + 1, cnt, mask);
};
```

---
