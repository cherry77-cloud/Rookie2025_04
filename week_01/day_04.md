## 一. 哈希集合设计: 链地址法

哈希集合是一种基于哈希表的数据结构，用于存储不重复的元素。链地址法是解决哈希冲突的一种常见方法，通过在哈希表的每个槽中使用链表来存储具有相同哈希值的元素。

---
### 哈希函数
- 将元素映射到哈希表的一个槽中。
- 使用取模运算将元素的键值映射到固定范围的索引。

### 链地址法
- 每个槽中维护一个链表，用于存储具有相同哈希值的元素。
- 当发生哈希冲突时，将冲突的元素添加到对应槽的链表中。

### 操作
- **添加元素**：计算哈希值，将元素添加到对应槽的链表中（如果不存在）。
- **删除元素**：计算哈希值，从对应槽的链表中移除元素（如果存在）。
- **查找元素**：计算哈希值，在对应槽的链表中查找元素。

---

```c++
// 链地址法
class MyHashSet {
private:
    vector<list<int>> data;
    static const int base = 131;
    static int hash(int key) {
        int h = key % base;
        if (h < 0) h += base;
        return h;
    }

public:
    /** Initialize your data structure here. */
    MyHashSet() : data(base) {}
    
    void add(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return;
            }
        }
        data[h].push_back(key);
    }
    
    void remove(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                data[h].erase(it);
                return;
            }
        }
    }
    
    /** Returns true if this set contains the specified element */
    bool contains(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                return true;
            }
        }
        return false;
    }
};
```

---


## 二. 最小栈: 双栈法
最小栈是一种支持快速获取栈中最小元素的数据结构。通过使用两个栈（一个存储数据，另一个存储最小值），可以在 `O(1)` 时间复杂度内实现 `push`、`pop`、`top` 和 `getMin` 操作。

- 双栈结构：
  - 数据栈（`x_stack`）：存储所有入栈的元素。
  - 最小值栈（`min_stack`）：存储当前栈中的最小值。

- 同步操作：
  - 每次 `push` 操作时，将元素压入数据栈，同时将当前最小值压入最小值栈。
  - 每次 `pop` 操作时，同时弹出数据栈和最小值栈的栈顶元素。
  - `top` 操作返回数据栈的栈顶元素。
  - `getMin` 操作返回最小值栈的栈顶元素。

- 初始化：
  - 最小值栈初始化为 `INT_MAX`，确保第一个元素入栈时能够正确更新最小值。

---

```c++
class MinStack {
private:
    stack<int> x_stack;
    stack<int> min_stack;

public:
    MinStack() {
        min_stack.push(INT_MAX);
    }
    
    void push(int val) {
        x_stack.push(val);
        min_stack.push(min(val, min_stack.top()));
    }
    
    void pop() {
        x_stack.pop();
        min_stack.pop();
    }
    
    int top() {
        return x_stack.top();
    }
    
    int getMin() {
        return min_stack.top();
    }
};
```

## 字符串哈希

```c++
class StringHash {
private:
    using ULL = unsigned long long;
    string s;
    const ULL BASE = 13331;
    vector<ULL> prefix_hash;  // 存储字符串 s 的前缀哈希值，prefix_hash[i] 表示 s[0..i-1] 的哈希值
    vector<ULL> power;

    void precompute() {
        int n = s.size();
        prefix_hash.resize(n + 1);
        power.resize(n + 1);
        prefix_hash[0] = 0;
        power[0] = 1;
        for (int i = 1; i <= n; ++i) {
            power[i] = power[i - 1] * BASE;
            prefix_hash[i] = prefix_hash[i - 1] * BASE + s[i - 1]; // 自然溢出
        }
    }

public:
    StringHash(const string& str) : s(str) {
        precompute();
    }

    ULL getHash(int l, int r) {
        int len = r - l + 1;
        return prefix_hash[r + 1] - prefix_hash[l] * power[len];
    }

    bool compare(int l1, int r1, int l2, int r2) {
        if (r1 - l1 != r2 - l2) return false;
        return getHash(l1, r1) == getHash(l2, r2);
    }
};
```
---

## 字典树

```c++
class Trie {
private:
    vector<Trie*> children;
    bool isEnd;
public:
    Trie() : children(26), isEnd(false) {}
    
    void insert(string word) {
        Trie* node = this;
        for (char ch : word) {
            ch -= 'a';
            if (node->children[ch] == nullptr) {
                node->children[ch] = new Trie();
            }
            node = node->children[ch];
        }
        node->isEnd = true;
    }
    
    bool search(string word) {
        Trie* node = this;
        for (char ch : word) {
            ch -= 'a';
            if (node->children[ch] == nullptr) {
                return false;
            }
            node = node->children[ch];
        }
        return node->isEnd;
    }
    
    bool startsWith(string prefix) {
        Trie* node = this;
        for (char ch : prefix) {
            ch -= 'a';
            if (node->children[ch] == nullptr) {
                return false;
            }
            node = node->children[ch];
        }
        return true;
    }
};
```
