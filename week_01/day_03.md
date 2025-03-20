## 一. 高精度加法

### 普通版本
```c++
string addHighPrecision(const string& s1, const string& s2) {
    vector<int> num1, num2;

    // 将字符串转换为数字（逆序存储）
    for (int i = s1.length() - 1; i >= 0; i--) num1.push_back(s1[i] - '0');
    for (int i = s2.length() - 1; i >= 0; i--) num2.push_back(s2[i] - '0');

    vector<int> result;
    int carry = 0;

    // 逐位相加
    for (int i = 0; i < num1.size() || i < num2.size() || carry; i++) {
        int val1 = (i < num1.size()) ? num1[i] : 0;
        int val2 = (i < num2.size()) ? num2[i] : 0;
        int sum = val1 + val2 + carry;
        result.push_back(sum % 10);
        carry = sum / 10;
    }

    // 去除前导零
    while (result.size() > 1 && result.back() == 0) result.pop_back();

    // 将结果转换为字符串
    string resultStr;
    for (int i = result.size() - 1; i >= 0; i--) resultStr += to_string(result[i]);

    return resultStr;
}
```
### 压`9`位版本
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

### 普通版本
```c++
```

### 压`9`位版本
```c++
string subtractHighPrecision(const string& s1, const string& s2) {
    const int BASE = 1e9;  // 压 9 位

    // 比较两个大整数的大小
    auto isGreaterOrEqual = [](const string& a, const string& b) {
        if (a.length() != b.length()) return a.length() > b.length();
        return a >= b;
    };

    // 将字符串按每 9 位分割成数字
    auto split = [](const string& s, vector<int>& nums) {
        for (int i = s.length(); i > 0; i -= 9)
            nums.push_back(stoi(s.substr(max(0, i - 9), i - max(0, i - 9))));
    };

    // 如果 s1 < s2，返回负数结果
    if (!isGreaterOrEqual(s1, s2))
        return "-" + subtractHighPrecision(s2, s1);

    vector<int> num1, num2;
    split(s1, num1);
    split(s2, num2);

    int borrow = 0;
    vector<int> result;

    // 逐块相减
    for (int i = 0; i < num1.size(); i++) {
        int val1 = num1[i];
        int val2 = (i < num2.size()) ? num2[i] : 0;
        int diff = val1 - val2 - borrow;

        if (diff < 0) {
            diff += BASE;
            borrow = 1;
        } else {
            borrow = 0;
        }

        result.push_back(diff);
    }

    // 去除前导零
    while (result.size() > 1 && result.back() == 0)
        result.pop_back();

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

    return resultStr.empty() ? "0" : resultStr;
}
```
