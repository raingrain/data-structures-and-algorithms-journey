# 返回字符串数列中第n项的第k个字符

## 网易

> - ***Question***
>   - 有如下规定：
>     - `L[1]` 对应 `a` ， `L[2]` 对应 `b` ， `L[3]` 对应 `c` ，...， `L[25]` 对应 `y` 。
>     - `S1 = a, S(i) = S(i-1) + L[i] + reverse(invert(S(i - 1)));`
>     - `invert` 操作定义为：
>       - 假设 `invert(S(2)) = 甲乙丙` 。
>       - `a + 甲 = 26` ，那么 `甲 = 26 - 1 = 25 -> y` 。
>       - `b + 乙 = 26` ，那么 `乙 = 26 - 2 = 24 -> x` 。
>       - `y + 丙 = 26` ，那么 `丙 = 26 - 25 = 1 -> a` 。
>       - 如上就是每一位的计算方式，所以 `invert(S2) = yxa, S3 = S2 + L[3] + reverse(invert(S2)) = aby + c + axy = abycaxy, invert(abycaxy) = yxawyba, then reverse = abywaxy, so S4 = abycaxy + d + abywaxy = abycaxydabywaxy` 。
>   - 字符串数列直到 `S25` 结束。
>   - 给定两个参数 `n` 和 `k` ，返回 `Sn` 的第 `k` 位是什么字符， `n` 从 `1` 开始， `k` 从 `1` 开始。

---

## *Java*

> - ***暴力递归***

```java
class Solution {
    
    // lens[i]表示Si长度为多少
    public static int[] lens = null;
    
    // 生成长度对应数组
    public static void fillLens() {
        lens = new int[26];
        lens[1] = 1;
        for (int i = 2; i <= 25; i++) {
            lens[i] = (lens[i - 1] << 1) + 1;
        }
    }
    
    // 求sn中的第k个字符
    // O(n), s <= 25 O(1)
    public static char kth(int n, int k) {
        if (lens == null) {
            fillLens();
        }
        if (n == 1) { 
            // 无视k
            return 'a';
        }
        // sn half
        int half = lens[n - 1];
        if (k <= half) {
            // 去Sn-1中找
            return kth(n - 1, k);
        } else if (k == half + 1) {
            // Ln
            return (char) ('a' + n - 1);
        } else {
            // sn
            // 我需要右半区，从左往右的第a个
            // 需要找到，s(n-1)从右往左的第a个
            // 当拿到字符之后，invert一下，就可以返回了！
            return invert(kth(n - 1, ((half + 1) << 1) - k));
        }
    }
    
    // 模拟invert操作
    public static char invert(char c) {
        return (char) (('a' << 1) + 24 - c);
    }
    
    // 为了测试
    public static String generateString(int n) {
        String s = "a";
        for (int i = 2; i <= n; i++) {
            s = s + (char) ('a' + i - 1) + reverseInvert(s);
        }
        return s;
    }
    
    // 为了测试
    public static String reverseInvert(String s) {
        char[] invert = invert(s).toCharArray();
        for (int l = 0, r = invert.length - 1; l < r; l++, r--) {
            char tmp = invert[l];
            invert[l] = invert[r];
            invert[r] = tmp;
        }
        return String.valueOf(invert);
    }
    
    // 为了测试
    public static String invert(String s) {
        char[] str = s.toCharArray();
        for (int i = 0; i < str.length; i++) {
            str[i] = invert(str[i]);
        }
        return String.valueOf(str);
    }
    
    // 为了测试
    public static void main(String[] args) {
        int n = 20;
        String str = generateString(n);
        int len = str.length();
        System.out.println("测试开始");
        for (int i = 1; i <= len; i++) {
            if (str.charAt(i - 1) != kth(n, i)) {
                System.out.println(i);
                System.out.println(str.charAt(i - 1));
                System.out.println(kth(n, i));
                System.out.println("出错了！");
                break;
            }
        }
        System.out.println("测试结束");
    }
    
}
```
