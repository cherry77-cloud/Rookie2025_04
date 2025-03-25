## 一. 指数型，排列型，组合型回溯

```cpp
void dfs(int u) {
    if (u == n) {
        subset.push_back(path);
        return;
    }
    
    // 不选
    dfs(u + 1);
    
    // 选
    path.push_back(u + 1);
    dfs(u + 1);
    path.pop_back();
}
```
