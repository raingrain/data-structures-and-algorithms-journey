# 使用指定字符拼出回文子串数量等于x的字符串

## 左老师课堂讲述

> - ***Question***
>   - 返回用 `r, e, d` 三种字符，拼出一个回文子串数量等于 `x` 的字符串。

---

## *Java*

> - ***贪心***

```java
class Solution {

    // 只用一个字符构成的字符串，回文子串的数量有规律
    // r 1
    // rr 2 + 1
    // rrr 3 + 2 + 1
    // rrrr 4 + 3 + 2 + 1
    // 个数为字符串长度作为项数的等差数列的和
    // 那么要拼出长度为x
    // 先选出a个r
    // 如果没凑够，再选b个e
    // 如果没凑够，再选c个d
    // 如果没凑够，再选e个r
    // ...
    // 选择的数数字保证构成的回文子串数量无限接近要求即可
    // 比如x等于9
    // 选rrr + ee
    public static String palindromeX(int x) {
        StringBuilder builder = new StringBuilder();
        char cur = 'r';
        while (x > 0) {
            int number = near(x);
            for (int i = 0; i < number; i++) {
                builder.append(cur);
            }
            x -= number * (number + 1) / 2;
            cur = cur == 'r' ? 'e' : (cur == 'e' ? 'd' : 'r');
        }
        return builder.toString();
    }

    public static int near(int x) {
        int l = 1;
        int r = x;
        int m, ans = 0;
        while (l <= r) {
            m = (l + r) / 2;
            if (ok(m, x)) {
                ans = m;
                l = m + 1;
            } else {
                r = m - 1;
            }
        }
        return ans;
    }

    public static boolean ok(int n, int x) {
        return ((long) n * (n + 1) / 2) <= x;
    }

    public static void main(String[] args) {
        int x = 27380;
        System.out.println(palindromeX(x));
    }

}
```

---

> ***last change: 2023/5/27***

---
