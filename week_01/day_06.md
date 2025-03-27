## 一. 位运算

```cpp
auto hamming_weight1 = [](int x) -> int {
    int ret = 0;
    while (x) {
        ret++;
        x = x & (x - 1);
    }
    return ret;
};

auto hamming_weight2 = [](int x) -> int {
    int ret = 0;
    while (x) {
        ret++;
        x -= x & -x;
    }
    return ret;
};
```
