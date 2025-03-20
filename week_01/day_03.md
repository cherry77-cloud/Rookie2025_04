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

## 三. 高精度乘法

```c++
string multiplyHighPrecision(const string& s1, const string& s2) {
    const int BASE = 1e9;  // 压 9 位

    // 将字符串按每 9 位分割成数字
    auto split = [](const string& s, vector<int>& nums) {
        for (int i = s.length(); i > 0; i -= 9)
            nums.push_back(stoi(s.substr(max(0, i - 9), i - max(0, i - 9))));
    };

    vector<int> num1, num2;
    split(s1, num1);
    split(s2, num2);

    // 结果数组
    vector<long long> result(num1.size() + num2.size(), 0);

    // 逐块相乘
    for (int i = 0; i < num1.size(); i++) {
        for (int j = 0; j < num2.size(); j++) {
            long long product = (long long)num1[i] * num2[j];
            result[i + j] += product;
            result[i + j + 1] += result[i + j] / BASE;
            result[i + j] %= BASE;
        }
    }

    // 处理进位
    for (int i = 0; i < result.size() - 1; i++) {
        result[i + 1] += result[i] / BASE;
        result[i] %= BASE;
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
            snprintf(buffer, sizeof(buffer), "%09lld", result[i]);
            resultStr += buffer;
        }
    }

    return resultStr;
}
```

---

## 四. 高精度除法

```c++
pair<string, long long> divideHighPrecision(const string& dividend, long long divisor) {
    long long remainder = 0;  // 余数
    string quotient;          // 商

    // 逐位处理被除数（从高位到低位）
    for (char c : dividend) {
        remainder = remainder * 10 + (c - '0');  // 更新余数
        quotient.push_back(remainder / divisor + '0');  // 计算当前位的商
        remainder %= divisor;  // 更新余数
    }

    // 去除商的前导零
    quotient.erase(0, quotient.find_first_not_of('0'));
    if (quotient.empty()) quotient = "0";  // 商为0的情况

    return {quotient, remainder};
}
```
