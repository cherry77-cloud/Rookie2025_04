## 一. Knuth-Morris-Pratt
`KMP`算法是一种高效的字符串匹配算法，用于在一个主字符串（`query`）中查找一个模式字符串（`pattern`）的所有出现位置。其核心思想是通过预处理模式串，构建一个 前缀函数（`fail` 数组），在匹配失败时利用前缀函数跳过不必要的比较，从而将时间复杂度优化到 `O(n + m)`，其中 `n` 是主字符串的长度，`m` 是模式串的长度。

### 核心思想
- **前缀函数**：前缀函数记录模式串中每个位置的最长相同前缀和后缀的长度。构建前缀函数的时间复杂度为 `O(m)`。
- **匹配过程**：使用前缀函数在匹配失败时快速跳过不必要的比较。匹配过程的时间复杂度为 `O(n)`。


---

```cpp
vector<int> kmp(const string& query, const string& pattern) {
    int n = query.size();
    int m = pattern.size();
    vector<int> result;

    // 1. 计算模式串 pattern 的前缀函数 fail
    vector<int> fail(m, -1);
    for (int i = 1; i < m; ++i) {
        int j = fail[i - 1];
        while (j != -1 && pattern[j + 1] != pattern[i]) {
            j = fail[j];
        }
        if (pattern[j + 1] == pattern[i]) {
            fail[i] = j + 1;
        }
    }

    // 2. 在字符串 query 中查找模式串 pattern
    int match = -1;
    for (int i = 0; i < n; ++i) {
        while (match != -1 && pattern[match + 1] != query[i]) {
            match = fail[match];
        }
        if (pattern[match + 1] == query[i]) {
            ++match;
        }
        if (match == m - 1) {
            result.push_back(i - m + 1);
            match = fail[match];
        }
    }

    return result;
}

vector<int> computeNextVal(const string& pattern) {
    int m = pattern.size();
    vector<int> nextval(m, -1); 
    int j = -1;
    for (int i = 1; i < m; ++i) {
        while (j != -1 && pattern[j + 1] != pattern[i]) {
            j = nextval[j];
        }
        if (pattern[j + 1] == pattern[i]) {
            j++;
        }
        nextval[i] = j;
        // 直接优化：如果回退后的字符相同，直接跳转到更早的位置
        if (nextval[i] != -1 && pattern[nextval[i] + 1] == pattern[i + 1]) {
            nextval[i] = nextval[nextval[i]];
        }
    }
    return nextval;
}
```
---

## 二. 字符串哈希

字符串哈希是一种将字符串映射为唯一整数值的技术，常用于快速比较字符串的子串是否相等。通过预处理字符串的前缀哈希值，可以在 `O(1)` 时间复杂度内计算任意子串的哈希值。

### 哈希函数：
- 将字符串的每个字符视为一个数字（如 `ASCII` 值），通过多项式哈希的方式计算哈希值。
- 使用一个基数（`BASE`，通常选择一个质数）和自然溢出（利用无符号整数的溢出特性）来减少冲突。

### 前缀哈希：
- 预处理字符串的前缀哈希值，`prefix_hash[i]` 表示字符串 `s[0..i-1]` 的哈希值。
- 通过前缀哈希值，可以快速计算任意子串的哈希值。

### 幂次数组：
- 预处理基数的幂次值，`power[i]` 表示 `BASE^i`。
- 用于在计算子串哈希时快速调整前缀哈希的权重。
---

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

## 三. 字典树
`Trie` 是一种用于高效存储和检索字符串的数据结构。它通过共享前缀来减少存储空间，并支持快速的插入、查找和前缀匹配操作。
### 节点结构：
- 每个节点包含一个长度为 `26` 的子节点数组（对应 `26` 个小写字母）。
- 一个布尔值 `isEnd`，表示当前节点是否是一个单词的结尾。

### 插入操作：
- 从根节点开始，逐个字符插入单词。
- 如果字符对应的子节点不存在，则创建一个新节点。
- 插入完成后，将最后一个节点的 `isEnd` 标记为 `true`。

### 查找操作：
- 从根节点开始，逐个字符查找单词。
- 如果字符对应的子节点不存在，则返回 `false`。
- 如果所有字符都存在，则检查最后一个节点的 `isEnd` 是否为 `true`。

### 前缀匹配操作：
- 从根节点开始，逐个字符查找前缀。
- 如果字符对应的子节点不存在，则返回 `false`。
- 如果所有字符都存在，则返回 `true`。
---

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

---
