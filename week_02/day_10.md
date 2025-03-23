## 一. 并查集

```cpp
class UnionFind {
private:
    vector<int> fa; // 父节点数组
    vector<int> rk; // 秩数组（树的高度）

public:
    UnionFind(int n) {
        fa.resize(n + 1);
        rk.resize(n + 1, 1);
        for (int i = 1; i <= n; ++i) {
            fa[i] = i;
        }
    }

    int find(int x) {
        if (fa[x] != x)
            fa[x] = find(fa[x]);
        return fa[x];
    }

    // 按秩合并
    void merge(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);
        if (rootA == rootB) return;

        if (rk[rootA] > rk[rootB])  fa[rootB] = rootA;
        else {
            fa[rootA] = rootB;
            if (rk[rootA] == rk[rootB])  rk[rootB]++;
        }
    }

    // 判断两个节点是否连通
    bool isConnected(int a, int b) {
        return find(a) == find(b);
    }
};
```
