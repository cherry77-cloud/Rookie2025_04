## 一. 并查集

### 并查集概述
- 并查集是一种用于管理元素分组的数据结构，支持以下两种操作：
- 查找（`Find`）：查找某个元素所属的集合（通常用根节点表示）。
- 合并（`Union`）：将两个元素所在的集合合并为一个集合。
- 并查集常用于解决动态连通性问题，判断两个元素是否属于同一集合，合并两个集合，统计集合的数量。

### 核心思想
- 并查集通过树结构表示集合，每个集合用一棵树表示，树的根节点作为集合的代表元。
- **初始化**，每个元素初始时是一个独立的集合，父节点指向自己。
- **查找**，查找某个元素所在集合的根节点。通过路径压缩优化，将查找路径上的所有节点直接指向根节点。
- **合并**，将两个元素所在的集合合并为一个集合。通过按秩合并优化，将较小的树合并到较大的树上，避免树的高度过高。

---

#### 递归版本
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

    bool isConnected(int a, int b) {
        return find(a) == find(b);
    }
};
```

---

#### 非递归版本
```cpp
class UnionFind {
private:
    vector<int> fa;

public:
    UnionFind(int n) {
        fa.resize(n + 1);
        for (int i = 1; i <= n; ++i) {
            fa[i] = i;
        }
    }

    int find(int x) {
        int root = x;
        while (fa[root] != root)
            root = fa[root];

        while (x != root) {
            int parent = fa[x];
            fa[x] = root;
            x = parent;
        }
        return root;
    }

    void merge(int a, int b) {
        int rootA = find(a);
        int rootB = find(b);
        if (rootA != rootB) {
            fa[rootA] = rootB;
        }
    }

    bool isConnected(int a, int b) {
        return find(a) == find(b);
    }
};
```

---
