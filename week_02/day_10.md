## 一. 并查集

### 并查集概述
- 并查集是一种用于管理元素分组的数据结构，支持以下两种操作：
- 查找（`Find`）：查找某个元素所属的集合（通常用根节点表示）。
- 合并（`Union`）：将两个元素所在的集合合并为一个集合。
- 并查集常用于解决动态连通性问题，判断两个元素是否属于同一集合，合并两个集合，统计集合的数量。

### 核心思想
- 并查集通过树结构表示集合，每个集合用一棵树表示，**树的根节点作为集合的代表元**。
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
        for (int i = 0; i <= n; ++i) {
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
        for (int i = 0; i <= n; ++i) {
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

## 二. 堆求解 `TopK` 问题的一般思路

堆结构能高效维护当前最优的K个元素，通过不断淘汰不符合条件的元素来解决问题。关键在于：
- 选择合适的堆类型（最大堆或最小堆）
- 维护固定大小的堆（K个元素）
- 动态更新堆内容

### 求 `TopK` 小元素

```cpp
// 维护大小为K的最大堆
// 遇到比堆顶小的元素时，替换堆顶
// 最终堆中保留最小的K个数

priority_queue<int> max_heap;

for (int num : nums) {
    if (max_heap.size() < k) {
        max_heap.push(num);
    } else if (num < max_heap.top()) {
        max_heap.pop();
        max_heap.push(num);
    }
}
```

### 求 `TopK` 大元素

```cpp
// 维护大小为K的最小堆
// 遇到比堆顶大的元素时，替换堆顶
// 最终堆中保留最大的K个数

priority_queue<int, vector<int>, greater<int>> min_heap;

for (int num : nums) {
    if (min_heap.size() < k) {
        min_heap.push(num);
    } else if (num > min_heap.top()) {
        min_heap.pop();
        min_heap.push(num);
    }
}
```

---

## 三. 二叉堆

### 功能概述

- 插入元素（`push`）：将新元素添加到堆中，并调整堆结构。
- 删除堆顶元素（`pop`）：移除最小元素，并调整堆结构。
- 获取堆顶元素（`top`）：返回当前堆中的最小值。
- 判空与大小（`empty`、`size`）：判断堆是否为空，获取堆中元素数量。

### 核心方法

- `heapifyUp` 功能：将索引 `p` 处的元素向上调整，直到堆满足最小堆性质。若当前节点值小于父节点值，则交换两者位置。重复上一步，直到到达根节点或父节点值更小。
- `heapifyDown` 功能：将索引 `u` 处的元素向下调整，直到堆满足最小堆性质。找到当前节点与其左右子节点中的最小值。若最小值是子节点，则交换两者位置。重复直到当前节点是子树中的最小值。

---

```cpp
class MinHeap {
private:
    vector<int> heap;
    int heapSize;

    void heapSwap(int a, int b) {
        swap(heap[a], heap[b]);
    }

    void heapifyUp(int p) {
        while (p > 1 && heap[p] < heap[p / 2]) {
            heapSwap(p, p / 2);
            p /= 2;
        }
    }

    void heapifyDown(int u) {
        int smallest = u;
        if (u * 2 <= heapSize && heap[u * 2] < heap[smallest]) smallest = u * 2;
        if (u * 2 + 1 <= heapSize && heap[u * 2 + 1] < heap[smallest]) smallest = u * 2 + 1;
        if (smallest != u) {
            heapSwap(u, smallest);
            heapifyDown(smallest);
        }
    }

public:
    MinHeap() {
        heap.push_back(0);  // 添加哨兵节点
        heapSize = 0;
    }

    void push(int value) {
        heap.push_back(value);
        heapSize++;
        heapifyUp(heapSize);
    }

    void pop() {
        heapSwap(1, heapSize);
        heap.pop_back();
        heapSize--;
        if (heapSize > 0) heapifyDown(1);
    }

    int top() const { return heap[1]; }
    bool empty() const { return heapSize == 0; }
    size_t size() const { return heapSize; }
};
```

---

## 四. 对顶堆 + 数据流中的中位数

### 核心思想与作用
对顶堆（`Two Heaps`）是一种高效维护动态数据流中位数的数据结构，它通过两个堆的协同工作实现：

- 大根堆（`left`）：存储较小的一半数字，堆顶是这部分的最大值
- 小根堆（`right`）：存储较大的一半数字，堆顶是这部分的最小值
- 通过保持两堆的大小关系，可以在`O(1)`时间内获取中位数，插入操作仅需`O(logn)`时间。

### 平衡策略

- 两堆相等时：
  - 新元素→右堆→取右堆顶→放入左堆
  - 结果：左堆比右堆多1个元素

- 左堆更大时：
  - 新元素→左堆→取左堆顶→放入右堆
  - 结果：两堆大小相等

---

```cpp
class MedianFinder {
private:
    priority_queue<int> left;  // 大根堆
    priority_queue<int, vector<int>, greater<int>> right;  // 小根堆
public:
    MedianFinder() {}
    
    void addNum(int num) {
        if (left.size() == right.size()) {
            right.push(num);
            left.push(right.top());
            right.pop();
        } else {
            left.push(num);
            right.push(left.top());
            left.pop();
        }
    }
    
    double findMedian() {
        if (left.size() > right.size()) {
            return left.top();
        }
        return (left.top() + right.top()) / 2.0;
    }
};
```
