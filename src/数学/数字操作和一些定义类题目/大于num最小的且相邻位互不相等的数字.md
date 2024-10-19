# 大于num最小的且相邻位互不相等的数字

## 微软

> - ***Question***
>   - 给定一个正数 `num` ，要返回一个大于 `num` 的数，并且每一位和相邻位的数字不能相等，返回达标的数字中，最小的那个。
>   - ***tips:***
>     - `num <= 10^9`

## Java

> - ***找规律***
>   - 从左往右遍历，找出最先相同的两个，把后面那位加一，然后后面全部变成 `0` ，然后再遍历一遍字符数组，重复如上过程。

```java
class Solution {

    public static int near(int num) {
        // num = 174
        // "0175"
        // num = 899
        // "0900"
        // num = 999
        // "01000"
        // 先加1，然后前面补0转字符数组
        char[] raw = ("0" + (num + 1)).toCharArray();
        process(raw);
        return Integer.parseInt(String.valueOf(raw));
    }

    // 99999
    // 100 000
    // 事实上可以优化，因为多个0会优化成010101，这是最小的
    public static void process(char[] raw) {
        for (int i = 1; i < raw.length; i++) {
            // 当前数字和上一个数字一样，当前数字+1
            if (raw[i - 1] == raw[i]) {
                addOne(raw, i);
                // 后面全刷成0
                for (int j = i + 1; j < raw.length; j++) {
                    raw[j] = '0';
                }
                process(raw);
                return;
            }
        }
    }

    // 加1并考虑进位
    public static void addOne(char[] r, int i) {
        // 比如0999+1
        // 0990
        // 0900
        // 0000
        // 1000
        while (r[i] == '9') {
            r[i--] = '0';
        }
        r[i]++;
    }

    public static void main(String[] args) {
        char[] test = new char[]{'0', '1', '2', '3'};

        System.out.println(Integer.valueOf(String.valueOf(test)));

        int num1 = 55;
        System.out.println(near(num1));

        int num2 = 1765;
        System.out.println(near(num2));

        int num3 = 98;
        System.out.println(near(num3));

        int num4 = 44432;
        System.out.println(near(num4));

        int num5 = 3298;
        System.out.println(near(num5));

        int num6 = 9999998;
        System.out.println(near(num6));
    }

}
```
