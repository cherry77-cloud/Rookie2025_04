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

## 二. 最小堆

### 功能概述

- 插入元素（`push`）：将新元素添加到堆中，并调整堆结构。
- 删除堆顶元素（`pop`）：移除最小元素，并调整堆结构。
- 获取堆顶元素（`top`）：返回当前堆中的最小值。
- 判空与大小（`empty`、`size`）：判断堆是否为空，获取堆中元素数量。

### 核心方法

- `heapifyUp` 功能：将索引 `p` 处的元素向上调整，直到堆满足最小堆性质。若当前节点值小于父节点值，则交换两者位置。重复上一步，直到到达根节点或父节点值更小。


- `heapifyDown` 功能：将索引 `u` 处的元素向下调整，直到堆满足最小堆性质。找到当前节点与其左右子节点中的最小值。若最小值是子节点，则交换两者位置。重复直到当前节点是子树中的最小值。

```cpp
class MinHeap {
private:
    vector<int> heap;

    void heapifyUp(int p) {
        while (p > 0 && heap[p] < heap[(p - 1) / 2]) {
            swap(heap[p], heap[(p - 1) / 2]);
            p = (p - 1) / 2;
        }
    }

    void heapifyDown(int u) {
        int size = heap.size();
        while (true) {
            int left = 2 * u + 1, right = 2 * u + 2, smallest = u;
            if (left < size && heap[left] < heap[smallest]) smallest = left;
            if (right < size && heap[right] < heap[smallest]) smallest = right;
            if (smallest == u) break;
            swap(heap[u], heap[smallest]);
            u = smallest;
        }
    }

public:
    void push(int value) {
        heap.push_back(value);
        heapifyUp(heap.size() - 1);
    }

    void pop() {
        heap[0] = heap.back();
        heap.pop_back();
        if (!heap.empty()) heapifyDown(0);
    }

    int top() const {
        return heap[0];
    }

    bool empty() const { return heap.empty(); }
    size_t size() const { return heap.size(); }
};
```

---
