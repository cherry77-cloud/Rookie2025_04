## 设计哈希集合

```c++
// 链地址法
class MyHashSet {
private:
    vector<list<int>> data;
    static const int base = 131;
    static int hash(int key) {
        return key % base;
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


## 最小栈

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
