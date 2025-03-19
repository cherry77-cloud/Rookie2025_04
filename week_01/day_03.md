## 一. 高精度加法

```c++
string addHighPrecision(const string& s1, const string& s2) {
    const int BASE = 1e9;  // 压 9 位
    vector<int> num1, num2;

    // 将字符串按每 9 位分割成数字
    auto split = [](const string& s, vector<int>& nums) {
        for (int i = s.length(); i > 0; i -= 9) {
            int start = max(0, i - 9);
            nums.push_back(stoi(s.substr(start, i - start)));
        }
    };

    split(s1, num1);
    split(s2, num2);

    int carry = 0;
    vector<int> result;
    int maxLen = max(num1.size(), num2.size());

    // 逐位相加
    for (int i = 0; i < maxLen || carry; i++) {
        int val1 = (i < num1.size()) ? num1[i] : 0;
        int val2 = (i < num2.size()) ? num2[i] : 0;
        int sumVal = val1 + val2 + carry;
        result.push_back(sumVal % BASE);
        carry = sumVal / BASE;
    }

    // 将结果转换为字符串
    string resultStr;
    for (int i = result.size() - 1; i >= 0; i--) {
        if (i == result.size() - 1) {
            resultStr += to_string(result[i]);
        } else {
            char buffer[10];
            snprintf(buffer, sizeof(buffer), "%09d", result[i]);
            resultStr += buffer;
        }
    }

    return resultStr;
}
```

---

## 二. 高精度减法
