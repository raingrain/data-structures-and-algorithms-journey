# 字符串哈希

## [P3370 【模板】字符串哈希](https://www.luogu.com.cn/problem/P3370)

> - **Question 1**
>   - 给定 `N` 个字符串（第 `i` 个字符串长度为 `Mi` ，字符串内包含数字、大小写字母，大小写敏感），请求出 `N` 个字符串中共有多少个不同的字符串。
>   - **输入描述**
>     - 第一行包含一个整数 `N` ，为字符串的个数。接下来 `N` 行每行包含一个字符串，为所提供的字符串。
>   - **输出描述**
>     - 输出包含一行，包含一个整数，为不同的字符串个数。
>   - **Tips**
>     - `N <= 10`
>     - `Mi ≈ 1000`
>     - `Mmax <= 1500`

## [28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

> - **Question 2**
>   - 给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 `0` 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。
>   - **Tips**
>     - `1 <= haystack.length, needle.length <= 10^4`
>     - `haystack` 和 `needle` 仅由小写英文字符组成

## Java

> - **long类型 + 同余定理 + 质数进制**
>   - 一种快速比对两个字符串的相等长度的字串是否相等的方法。

```java
// 哈希函数的用处：把复杂样本变成数字，以后复杂样本之间的对比，就变成了数字之间的对比

// 哈希函数的基本性质
// 1. 输入参数的可能性是无限的，输出的值范围相对有限
// 2. 输入同样的样本一定得到同样的输出值，也就是哈希函数没有任何随机机制
// 3. 输入不同的样本也可能得到同样的输出值，此时叫哈希碰撞
// 4. 输入大量不同的样本，得到的大量输出值，会几乎均匀的分布在整个输出域上（不管有没有规律，也不管多么相似，总之一堆不同的输入，得到的输出结果从分布上看，熵最大！）

// 哈希函数的种类很多，但都符合上述性质
// 性质4是最重要的，哈希碰撞理论上无法避免，但是好的哈希函数会让碰撞几率变的很低

// 哈希函数的算法有很多，字符串哈希是其中最常用的，也是唯一需要掌握代码实现的哈希函数

// 字符串哈希：如何得到整个字符串的哈希值
// 1. 理解long类型自然溢出，计算加、减、乘时，自然溢出后的状态等同于对2的64次方取模的值状态
// 2. 字符串转化成base进制的数字并让其自然溢出
// 3. 选择base为一个质数，比如499、131、1313等，为了自然溢出后的状态尽量分散
// 4. 转化时让每一位的值从1开始，不从0开始，这样就得到了一个long类型的数字代表字符串
// 5. 利用数字的比较去替代字符串的比较，可以大大减少复杂度

// 字符串哈希从理论上说会有碰撞导致出错，但现实中的算法考察样本量太少了，出错概率非常低，即便是出错了，也可以更换进制数base，再去赌，一定能赌赢。
// 没错！是玄学！但是好用！堪称赌狗的胜利！

// 字符串哈希原理和实现
// 比如p = 233, 也就是选择的质数进制
// " 3 1 2 5 6 ..."
//   0 1 2 3 4
// hash[0] = 3 * p的0次方
// hash[1] = 3 * p的1次方 + 1 * p的0次方
// hash[2] = 3 * p的2次方 + 1 * p的1次方 + 2 * p的0次方
// hash[3] = 3 * p的3次方 + 1 * p的2次方 + 2 * p的1次方 + 5 * p的0次方
// hash[4] = 3 * p的4次方 + 1 * p的3次方 + 2 * p的2次方 + 5 * p的1次方 + 6 * p的0次方
// 所以hash[i] = hash[i-1] * p + arr[i]，这个方式就可以得到上面说的意思
// 当你想得到子串"56"的哈希值
// 子串"56"的哈希值 = hash[4] - hash[2] * p的2次方(就是子串"56"的长度次方)
// hash[4] = 3 * p的4次方 + 1 * p的3次方 + 2 * p的2次方 + 5 * p的1次方 + 6 * p的0次方
// hash[2] = 3 * p的2次方 + 1 * p的1次方 + 2 * p的0次方
// hash[2] * p的2次方 = 3 * p的4次方 + 1 * p的3次方 + 2 * p的2次方
// 所以hash[4] - hash[2] * p的2次方 = 5 * p的1次方 + 6 * p的0次方
// 这样就得到子串"56"的哈希值了
// 所以，子串s[l...r]的哈希值 = hash[r] - hash[l-1] * p的(r-l+1)次方
// 也就是说，hash[l-1] * p的(r-l+1)次方，正好和hash[r]所代表的信息，前面对齐了
// 减完之后，正好就是子串s[l...r]的哈希值
class Solution {

    // 暴力方法
    // 为了验证
    public static boolean rightCheck(String str, int l1, int l2, int len) {
        if (l1 + len > str.length() || l2 + len > str.length()) {
            return false;
        }
        if (l1 == l2) {
            return true;
        }
        return str.substring(l1, l1 + len).equals(str.substring(l2, l2 + len));
    }

    // 哈希方法检测
    public static int MAXN = 100005;

    // pow[i]代表base的i次方
    public static long[] pow = new long[MAXN];

    // 前缀哈希数组
    // hash[5]代表字符串前5个字符的哈希串
    public static long[] hash = new long[MAXN];

    // 碰撞了就换一个更大的质数
    public static int base = 499;

    public static void build(String str, int n) {
        // k
        pow[0] = 1;
        for (int j = 1; j < n; j++) {
            pow[j] = pow[j - 1] * base;
        }
        // a -> 1
        // b -> 2
        // c -> 3
        // z -> 26
        // 前缀和的哈希值
        // str[0] = str[0] - 'a' + 1
        hash[0] = str.charAt(0) - 'a' + 1;
        for (int j = 1; j < n; j++) {
            // base : 进制
            hash[j] = hash[j - 1] * base + str.charAt(j) - 'a' + 1;
        }
    }

    public static boolean hashCheck(int n, int l1, int l2, int len) {
        int r1 = l1 + len - 1;
        int r2 = l2 + len - 1;
        if (r1 >= n || r2 >= n) {
            return false;
        }
        return hash(l1, r1) == hash(l2, r2);
    }

    // str[l...r] 的哈希值！告诉我！
    public static long hash(int l, int r) {
        // 0~6
        // 2~6
        // h[6] - h[1] * k的5次方
        long ans = hash[r];
        ans -= l == 0 ? 0 : (hash[l - 1] * pow[r - l + 1]);
        return ans;
    }

    // 为了测试
    public static String randomString(int len, int v) {
        char[] str = new char[len];
        for (int i = 0; i < len; i++) {
            str[i] = (char) ('a' + (int) (Math.random() * v));
        }
        return String.valueOf(str);
    }

    // 为了测试
    public static void main(String[] args) {
        String test = "abcabcabcabcabcabcabcabc";
        int size = test.length();
        build(test, size);
        System.out.println(hashCheck(size, 6, 15, 3));

        System.out.println("测试开始");
        int N = 10000;
        int V = 3;
        int testTeams = 100;
        int testTimes = 5000;
        int LEN = 6;
        for (int i = 0; i < testTeams; i++) {
            int n = (int) (Math.random() * N) + 1;
            String str = randomString(n, V);
            build(str, n);
            for (int k = 0; k <= testTimes; k++) {
                int l1 = (int) (Math.random() * n);
                int l2 = (int) (Math.random() * n);
                int len = (int) (Math.random() * LEN) + 1;
                boolean ans1 = rightCheck(str, l1, l2, len);
                boolean ans2 = hashCheck(n, l1, l2, len);
                if (ans1 != ans2) {
                    System.out.println("出错了!");
                    break;
                }
            }
        }
        System.out.println("测试结束");
    }

}

// Question 1
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 10001;

    public static int base = 499;

    public static long[] nums = new long[MAXN];

    public static int n;

    public static void main(String[] args) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = Integer.valueOf(in.readLine());
        for (int i = 0; i < n; i++) {
            nums[i] = value(in.readLine().toCharArray());
        }
        out.println(cnt());
        out.flush();
        out.close();
        in.close();
    }

    public static long value(char[] s) {
        long ans = v(s[0]);
        for (int i = 1; i < s.length; i++) {
            ans = ans * base + v(s[i]);
        }
        return ans;
    }

    // 数字 + 大写 + 小写
    // '0' -> 1
    // '1' -> 2
    // ...
    // '9' -> 10
    // 'A' -> 11
    // 'B' -> 12
    // ...
    // 'Z' -> 36
    // 'a' -> 37
    // ...
    // 'z' -> 62
    public static int v(char c) {
        if (c >= '0' && c <= '9') {
            return c - '0' + 1;
        } else if (c >= 'A' && c <= 'z') {
            return c - 'A' + 11;
        } else {
            return c - 'a' + 37;
        }
    }

    public static int cnt() {
        Arrays.sort(nums, 0, n);
        int ans = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i] != nums[i - 1]) {
                ans++;
            }
        }
        return ans;
    }

}

// Question 2
// KMP解法在另一个文件
// 字符串中子串对比变成哈希值对比非常好用的！大量节省时间
// 很多较难的算法都可以被字符串哈希替代，都是因为子串对比的代价变为O(1)
// 字符串哈希易于理解且使用灵活，因为非常方便的子串对比，很多难题变得非常好想
// 字符串哈希也能替代Manacher算法
// 不过时间复杂度没有Manacher算法解决回文类的问题好
// Manacher算法生成回文半径数组，时间复杂度O(n)
// 字符串哈希替代Manacher算法生成回文半径数组，时间复杂度O(n * logn)
class Solution {

    public static int strStr(String str1, String str2) {
        char[] s1 = str1.toCharArray();
        char[] s2 = str2.toCharArray();
        int n = s1.length;
        int m = s2.length;
        build(s1, n);
        long h2 = s2[0] - 'a' + 1;
        for (int i = 1; i < m; i++) {
            h2 = h2 * base + s2[i] - 'a' + 1;
        }
        for (int l = 0, r = m - 1; r < n; l++, r++) {
            if (hash(l, r) == h2) {
                return l;
            }
        }
        return -1;
    }

    // 如下代码是字符串哈希的原理和模版
    // 比如，base = 499, 也就是课上说的选择的质数进制
    // 再比如字符串s如下
    // " c a b e f "
    //   0 1 2 3 4
    // hash[0] = 3 * base的0次方
    // hash[1] = 3 * base的1次方 + 1 * base的0次方
    // hash[2] = 3 * base的2次方 + 1 * base的1次方 + 2 * base的0次方
    // hash[3] = 3 * base的3次方 + 1 * base的2次方 + 2 * base的1次方 + 5 * base的0次方
    // hash[4] = 3 * base的4次方 + 1 * base的3次方 + 2 * base的2次方 + 5 * base的1次方 + 6 *
    // base的0次方
    // hash[i] = hash[i-1] * base + s[i] - 'a' + 1，就是上面说的意思
    // 想计算子串"be"的哈希值 -> 2 * base的1次方 + 5 * base的0次方
    // 子串"be"的哈希值 = hash[3] - hash[1] * base的2次方(子串"be"的长度次方)
    // hash[1] = 3 * base的1次方 + 1 * base的0次方
    // hash[1] * base的2次方 = 3 * base的3次方 + 1 * base的2次方
    // hash[3] = 3 * base的3次方 + 1 * base的2次方 + 2 * base的1次方 + 5 * base的0次方
    // hash[3] - hash[1] * base的2次方 = 2 * base的1次方 + 5 * base的0次方
    // 这样就得到子串"be"的哈希值了
    // 子串s[l...r]的哈希值 = hash[r] - hash[l-1] * base的(r-l+1)次方，就是上面说的意思
    public static int MAXN = 100005;

    public static int base = 499;

    public static long[] pow = new long[MAXN];

    public static long[] hash = new long[MAXN];

    public static void build(char[] s, int n) {
        pow[0] = 1;
        for (int i = 1; i < n; i++) {
            pow[i] = pow[i - 1] * base;
        }
        hash[0] = s[0] - 'a' + 1;
        for (int i = 1; i < n; i++) {
            hash[i] = hash[i - 1] * base + s[i] - 'a' + 1;
        }
    }

    // 返回s[l...r]的哈希值
    public static long hash(int l, int r) {
        long ans = hash[r];
        if (l > 0) {
            ans -= hash[l - 1] * pow[r - l + 1];
        }
        return ans;
    }

}
```
