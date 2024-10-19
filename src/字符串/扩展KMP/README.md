# 扩展KMP

## [P5410 【模板】扩展 KMP/exKMP（Z 函数）](https://www.luogu.com.cn/problem/P5410)

> - ***Question***
>   - 给定两个字符串 `a, b` ，求出两个数组。
>   - `z` 数组： `b` 与 `b` 每一个后缀串的最长公共前缀长度。
>   - `e` 数组： `b` 与 `a` 每一个后缀串的最长公共前缀长度。
>   - 计算出要求的两个数组后，输出这两个数组的权值即可。
>   - `x[i]` 位置的权值定义为 `(i * (x[i] + 1))` 。
>   - ***输入描述***
>     - 两行两个字符串 `a, b` 。
>   - ***输出描述***
>     - 第一行一个整数，表示 `z` 的权值。
>     - 第二行一个整数，表示 `p` 的权值。
>   - ***tips:***
>     - `1 <= |a|, |b| <= 2 * 10^7`

## Java

> - ***派生于Manacher***

```java
// 理解扩展KMP的重要步骤
// 1. 理解z数组、理解匹配右边界r、理解匹配中心c，课上会图解
// 2. 理解扩展KMP的过程，当来到的出发点i，如何利用z、r、c来进扩展过程的加速，课上详细图解
//    a. i没有被r包住，那么以i为出发点直接扩展
//    b. i被r包住，但是前点 i-c 的扩展长度，对应在大扩展区域以内，直接确定z[i] = z[i-c]
//    c. i被r包住，但是前点 i-c 的扩展长度，对应在大扩展区域以外，直接确定z[i] = r - i
//    d. i被r包住，但是前点 i-c 的扩展长度，对应在大扩展区域的边界，从r之外的位置进行扩展
// 3. 理解了Manacher算法，可以轻易理解生成z数组的时间复杂度O(n)
// 4. 甚至代码都和Manacher算法高度相似，巧妙的while兼顾四种情况，代码中无需四种情况的判断
// 5. 理解了z数组的生成过程，那么e数组的生成过程同理，课上会图解
import java.io.*;

public class Main {

    public static int MAXN = 20000001;

    public static int[] z = new int[MAXN];

    public static int[] e = new int[MAXN];

    public static void main(String[] args) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        char[] a = in.readLine().toCharArray();
        char[] b = in.readLine().toCharArray();
        zArray(b, b.length);
        eArray(a, b, a.length, b.length);
        out.println(eor(z, b.length));
        out.println(eor(e, a.length));
        out.flush();
        out.close();
        in.close();
    }

    // 非常像Manacher算法
    public static void zArray(char[] s, int n) {
        z[0] = n;
        for (int i = 1, c = 1, r = 1, len; i < n; i++) {
            len = r > i ? Math.min(r - i, z[i - c]) : 0;
            while (i + len < n && s[i + len] == s[len]) {
                len++;
            }
            if (i + len > r) {
                r = i + len;
                c = i;
            }
            z[i] = len;
        }
    }

    // 非常像Manacher算法
    public static void eArray(char[] a, char[] b, int n, int m) {
        for (int i = 0, c = 0, r = 0, len; i < n; i++) {
            len = r > i ? Math.min(r - i, z[i - c]) : 0;
            while (i + len < n && len < m && a[i + len] == b[len]) {
                len++;
            }
            if (i + len > r) {
                r = i + len;
                c = i;
            }
            e[i] = len;
        }
    }

    public static long eor(int[] arr, int n) {
        long ans = 0;
        for (int i = 0; i < n; i++) {
            ans ^= (long) (i + 1) * (arr[i] + 1);
        }
        return ans;
    }

}
```
