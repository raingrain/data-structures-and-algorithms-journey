# 卡特兰数

## 公式

![image](./images/公式.png)

![image](./images/公式要求.png)

## 模型

![image](./images/模型1.png)

![image](./images/模型2.png)

![image](./images/模型3.png)

## 取模处理

![image](./images/取模处理.png)

## [P1044 [NOIP2003 普及组] 栈](https://www.luogu.com.cn/problem/P1044)

> - **Question 1**
>   - 进栈顺序规定为 `1, 2, 3, ..., n` ，返回有多少种不同的出栈顺序。测试题目的数据量很小，得到的卡特兰数没有多大，不需要取模处理，但是请假设，当 `n` 比较大时，卡特兰数是很大的，答案对 `1000000007` 取模。

## [P1976 鸡蛋饼](https://www.luogu.com.cn/problem/P1976)

> - **Question 2**
>   - 圆上有 `2n` 个点，这些点成对连接起来，形成 `n` 条线段，任意两条线段不能相交，返回连接的方法数注意！答案对 `10^8 + 7` 取模！
>   - **Tips**
>     - `1 <= n <= 2999`

## [P1722 矩阵 II](https://www.luogu.com.cn/problem/P1722)

> - **Question 3**
>   - 有 `n` 个红和 `n` 个黑，要组成 `2n` 长度的数列，保证任意前缀上，红的数量大于等于黑的数量，返回有多少种排列方法，答案对 `100` 取模。
>   - **Tips**
>     - `1 <= n <= 100`

## [96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

> - **Question 4**
>   - 给你一个整数 `n` ，求恰由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的二叉搜索树有多少种？返回满足题意的二叉搜索树的种数。
>   - **Tips**
>     - `1 <= n <= 19`

## [P3200 [HNOI2009] 有趣的数列](https://www.luogu.com.cn/problem/P3200)

> - **Question 5**
>   - 求第 `n` 项卡特兰数，要求答案对 `p` 取模。
>   - **Tips**
>     - `1 <= n <= 10^6`
>     - `1 <= p <= 10^9`

## [P1641 [SCOI2010] 生成字符串](https://www.luogu.com.cn/problem/P1641)

> - **Question 6**
>   - 有 `n` 个 `1` 和 `m` 个 `0` ，要组成 `n + m` 长度的数列，保证任意前缀上， `1` 的数量大于等于 `0` 的数量，返回有多少种排列方法，答案对 `20100403` 取模。
>   - **Tips**
>     - `1 <= m <= n <= 10^6`

## [SKYLINE - Skyline](https://www.luogu.com.cn/problem/SP7897)

> - **Question 7**
>   - 数字从 `1` 到 `n` ，可以形成很多排列，要求任意从左往右的三个位置，不能出现依次递增的样子，返回排列的方法数，答案对 `1000000` 取模。
>   - **Tips**
>     - `1 <= n <= 1000`

## [P2532 [AHOI2012] 树屋阶梯](https://www.luogu.com.cn/problem/P2532)

> - **Question 8**
>   - 地面高度是 `0` ，想搭建一个阶梯，要求每一个台阶上升 `1` 的高度，最终到达高度 `n` ，有无穷多任意规格的矩形材料，但是必须选择 `n` 个矩形，希望能搭建出阶梯的样子，返回搭建阶梯的不同方法数，答案可能很大，不取模！就打印真实答案。
>   - **Tips**
>     - `1 <= n <= 500`

## [P3978 [TJOI2015] 概率论](https://www.luogu.com.cn/problem/P3978)

> - **Question 9**
>   - 一共有 `n` 个节点，认为节点之间无差别，能形成很多不同结构的二叉树，假设所有不同结构的二叉树，等概率出现一棵，返回叶子节点的期望。
>   - **Tips**
>     - `1 <= n <= 10^9`
>     - 答案误差小于 `10^-9`

## [95. 不同的二叉搜索树 II](https://leetcode.cn/problems/unique-binary-search-trees-ii/)

> - **Question 10**
>   - 给你一个整数 `n` ，请你生成并返回所有由 `n` 个节点组成且节点值从 `1` 到 `n` 互不相同的不同 二叉搜索树。可以按任意顺序返回答案。
>   - **Tips**
>     - `1 <= n <= 8`

## Java

> - **Question 1: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static int MOD = 1000000007;

    public static int MAXN = 1000001;

    // 阶乘余数表
    public static long[] fac = new long[MAXN];

    // 阶乘逆元表
    public static long[] inv1 = new long[MAXN];

    // 连续数逆元表
    public static long[] inv2 = new long[MAXN];

    // 生成阶乘余数表、阶乘逆元表
    public static void build1(int n) {
        fac[0] = inv1[0] = 1;
        fac[1] = 1;
        for (int i = 2; i <= n; i++) {
            fac[i] = ((long) i * fac[i - 1]) % MOD;
        }
        inv1[n] = power(fac[n], MOD - 2);
        for (int i = n - 1; i >= 1; i--) {
            inv1[i] = ((long) (i + 1) * inv1[i + 1]) % MOD;
        }
    }

    // 生成连续数逆元表
    public static void build2(int n) {
        inv2[1] = 1;
        for (int i = 2; i <= n + 1; i++) {
            inv2[i] = MOD - inv2[MOD % i] * (MOD / i) % MOD;
        }
    }

    public static long power(long x, long p) {
        long ans = 1;
        while (p > 0) {
            if ((p & 1) == 1) {
                ans = (ans * x) % MOD;
            }
            x = (x * x) % MOD;
            p >>= 1;
        }
        return ans;
    }

    public static long c(int n, int k) {
        return (((fac[n] * inv1[k]) % MOD) * inv1[n - k]) % MOD;
    }

    // 公式1
    public static long compute1(int n) {
        build1(2 * n);
        return (c(2 * n, n) - c(2 * n, n - 1) + MOD) % MOD;
    }

    // 公式2
    public static long compute2(int n) {
        build1(2 * n);
        return c(2 * n, n) * power(n + 1, MOD - 2) % MOD;
    }

    // 公式3
    public static long compute3(int n) {
        build2(n);
        long[] f = new long[n + 1];
        f[0] = f[1] = 1;
        for (int i = 2; i <= n; i++) {
            f[i] = f[i - 1] * (4 * i - 2) % MOD * inv2[i + 1] % MOD;
        }
        return f[n];
    }

    // 公式4
    public static long compute4(int n) {
        long[] f = new long[n + 1];
        f[0] = f[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int l = 0, r = i - 1; l < i; l++, r--) {
                f[i] = (f[i] + f[l] * f[r] % MOD) % MOD;
            }
        }
        return f[n];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        out.println(compute1(n));
        //  out.println(compute2(n));
        //  out.println(compute3(n));
        //  out.println(compute4(n));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 2: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static int MOD = 100000007;

    public static int MAXN = 1000001;

    public static long[] fac = new long[MAXN];

    public static long[] inv = new long[MAXN];

    public static void build(int n) {
        fac[0] = inv[0] = 1;
        fac[1] = 1;
        for (int i = 2; i <= n; i++) {
            fac[i] = ((long) i * fac[i - 1]) % MOD;
        }
        inv[n] = power(fac[n], MOD - 2);
        for (int i = n - 1; i >= 1; i--) {
            inv[i] = ((long) (i + 1) * inv[i + 1]) % MOD;
        }
    }

    public static long power(long x, long p) {
        long ans = 1;
        while (p > 0) {
            if ((p & 1) == 1) {
                ans = (ans * x) % MOD;
            }
            x = (x * x) % MOD;
            p >>= 1;
        }
        return ans;
    }

    public static long c(int n, int k) {
        return (((fac[n] * inv[k]) % MOD) * inv[n - k]) % MOD;
    }

    // 这里用公式1
    public static long compute(int n) {
        build(2 * n);
        return (c(2 * n, n) - c(2 * n, n - 1) + MOD) % MOD;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        out.println(compute(n));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 3: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static int MOD = 100;

    // 因为取模的数字含有很多因子
    // 无法用费马小定理或者扩展欧几里得求逆元
    // 同时注意到n的范围并不大，直接使用公式4
    public static long compute(int n) {
        long[] f = new long[n + 1];
        f[0] = f[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int l = 0, r = i - 1; l < i; l++, r--) {
                f[i] = (f[i] + f[l] * f[r] % MOD) % MOD;
            }
        }
        return f[n];
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        out.println(compute(n));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 4: 卡特兰数**
>   - 这是一种数列，其通项公式主要有两种：
>     - `Cn = 1 if n = 0`
>     - `Cn = C(2n, n) - (C(2n, n + 1) or C(2n, n - 1))`
>     - `Cn = C0 * Cn-1 + C1 * Cn-2 + ... + Cn-2 * C1 + Cn-1 * C0`
>     - 代码递归实现：`Cn+1 = 2(2n + 1) * Cn / (n + 2)` or `Cn = (4 * n - 2) * Cn-1 / (n + 1)`
>   - 阶乘公式用于解决括号配对问题，出入栈次序个数问题，股票涨落后不亏问题。
>   - 乘法公式可用于给定节点组成二叉树问题。
>   - 还可以解决凸多边形划分三角形问题，在 `n x n` 格点中不越过对角线的单调路径的个数问题，有 `n + 1` 个叶子的满二叉树的个数。
>   - 如果题目解题时用到的公式和卡特兰数通项公式一样，那就直接根据题目给定的项数，直接返回卡特兰数列对应位置的值即可。

```java
class Solution {
    
    // 打表
    private final int[] CatalanNumber = {1, 1, 2, 5, 14, 42, 132, 429, 1430, 4862, 16796, 58786, 208012, 742900, 2674440, 9694845, 35357670, 129644790, 477638700, 1767263190};
    
    // 树的个数
    public int numTrees(int n) {
        // return CatalanNumber[n];
        return (int) getCatalanNumber(n);
    }
    
    // 递推式
    private long getCatalanNumber(int n) {
        if (n == 0) {
            return 0;
        }
        if (n == 1) {
            return 1;
        }
        return (4L * n - 2) * getCatalanNumber(n - 1) / (n + 1);
    }

    // 新版
    // 数据量小用哪个公式都可以
    // 不用考虑溢出、取模等问题
    // 同时注意到n的范围并不大，直接使用公式4
    public static int numTrees(int n) {
        int[] f = new int[n + 1];
        f[0] = f[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int l = 0, r = i - 1; l < i; l++, r--) {
                f[i] += f[l] * f[r];
            }
        }
        return f[n];
    }
    
}
```

> - **Question 5: 卡特兰数 + 因子计数法**
>   - ![image](./images/5.png)

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    public static int MAXN = 2000001;

    // 如果minpf[i] == 0，代表i是质数
    // 如果minpf[i] != 0，代表i是合数，并且最小质因子是minpf[i]
    public static int[] minpf = new int[MAXN];

    // 质数表
    public static int[] prime = new int[MAXN];

    // 质数表大小
    public static int cnt;

    // 因子计数
    public static int[] counts = new int[MAXN];

    // 欧拉筛，时间复杂度O(n)
    public static void euler(int n) {
        Arrays.fill(minpf, 2, n, 0);
        cnt = 0;
        for (int i = 2; i <= n; i++) {
            // minpf[i] == 0代表i为质数，收集进质数表
            if (minpf[i] == 0) {
                prime[cnt++] = i;
            }
            for (int j = 0; j < cnt; j++) {
                if (i * prime[j] > n) {
                    break;
                }
                // 此时收集(i * prime[j])这个数的最小质因子为prime[j]
                // minpf[i * prime[j]] != 0，也标记了(i * prime[j])是合数
                minpf[i * prime[j]] = prime[j];
                if (i % prime[j] == 0) {
                    break;
                }
            }
        }
    }

    public static long power(long x, long p, int mod) {
        long ans = 1;
        while (p > 0) {
            if ((p & 1) == 1) {
                ans = (ans * x) % mod;
            }
            x = (x * x) % mod;
            p >>= 1;
        }
        return ans;
    }

    // 使用的是公式2 + 质因子计数法
    public static int compute(int n, int mod) {
        // 利用欧拉筛生成[2 ~ 2*n]范围上所有数的最小质因子
        // 如果x为质数，minpf[x] == 0
        // 如果x为合数，x的最小质因子为minpf[x]
        euler(2 * n);
        // 分母每个因子设置计数
        Arrays.fill(counts, 2, n + 1, -1);
        // 分子每个因子设置计数
        Arrays.fill(counts, n + 2, 2 * n + 1, 1);
        // 从大到小的每个数统计计数
        // 合数根据最小质因子来分解，变成更小数字的计数
        // 质数无法分解，计数确定，最后快速幂计算乘积
        for (int i = 2 * n; i >= 2; i--) {
            if (minpf[i] != 0) {
                counts[minpf[i]] += counts[i];
                counts[i / minpf[i]] += counts[i];
                counts[i] = 0;
            }
        }
        // 每个质数的幂，都乘起来，就是最后答案
        long ans = 1;
        for (int i = 2; i <= 2 * n; i++) {
            if (counts[i] != 0) {
                ans = ans * power(i, counts[i], mod) % mod;
            }
        }
        return (int) ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int mod = (int) in.nval;
        out.println(compute(n, mod));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 6: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static int MOD = 20100403;

    public static int MAXN = 2000001;

    public static long[] fac = new long[MAXN];

    public static long[] inv = new long[MAXN];

    public static void build(int n) {
        fac[0] = inv[0] = 1;
        fac[1] = 1;
        for (int i = 2; i <= n; i++) {
            fac[i] = ((long) i * fac[i - 1]) % MOD;
        }
        inv[n] = power(fac[n], MOD - 2);
        for (int i = n - 1; i >= 1; i--) {
            inv[i] = ((long) (i + 1) * inv[i + 1]) % MOD;
        }
    }

    public static long power(long x, long p) {
        long ans = 1;
        while (p > 0) {
            if ((p & 1) == 1) {
                ans = (ans * x) % MOD;
            }
            x = (x * x) % MOD;
            p >>= 1;
        }
        return ans;
    }

    public static long c(int n, int k) {
        return (((fac[n] * inv[k]) % MOD) * inv[n - k]) % MOD;
    }

    public static long compute(int n, int m) {
        build(n + m);
        return (c(n + m, m) - c(n + m, m - 1) + MOD) % MOD;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        out.println(compute(n, m));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 7: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static int MOD = 1000000;

    public static int MAXN = 1001;

    public static long[] f = new long[MAXN];

    // 因为取模的数字含有很多因子
    // 无法用费马小定理或者扩展欧几里得求逆元
    // 同时注意到n的范围并不大，直接使用公式4
    public static void build() {
        f[0] = f[1] = 1;
        for (int i = 2; i < MAXN; i++) {
            for (int l = 0, r = i - 1; l < i; l++, r--) {
                f[i] = (f[i] + f[l] * f[r] % MOD) % MOD;
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        build();
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int n = (int) in.nval;
            if (n == 0) {
                break;
            }
            out.println(f[n]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 8: 卡特兰数**

```java
import java.io.*;
import java.math.BigInteger;

public class Main {

    // 这里用公式2
    // java使用BigInteger即可
    public static BigInteger compute(int n) {
        BigInteger a = new BigInteger("1");
        BigInteger b = new BigInteger("1");
        BigInteger cur;
        for (int i = 1; i <= 2 * n; i++) {
            cur = new BigInteger(String.valueOf(i));
            a = a.multiply(cur);
            if (i <= n) {
                b = b.multiply(cur);
            }
        }
        return a.divide(b.multiply(b)).divide(new BigInteger(String.valueOf(n + 1)));
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        BigInteger ans = compute(n);
        out.println(ans.toString());
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 9: 卡特兰数**

```java
import java.io.*;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        double n = in.nval;
        out.printf("%.9f", n * (n + 1) / ((2 * n - 1) * 2));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **Question 10: 回溯**
>   - 二叉搜索树关键的性质是根节点的值大于左子树所有节点的值，小于右子树所有节点的值，且左子树和右子树也同样为二叉搜索树。因此在生成所有可行的二叉搜索树的时候，假设当前序列长度为 nnn，如果我们枚举根节点的值为 `i` ，那么根据二叉搜索树的性质我们可以知道左子树的节点值的集合为 `[1 ~ i - 1]` ，右子树的节点值的集合为 `[i + 1 ~ n]` 。而左子树和右子树的生成相较于原问题是一个序列长度缩小的子问题，因此我们可以想到用回溯的方法来解决这道题目。
>   - 我们定义 `generateTrees(start, end)` 函数表示当前值的集合为 `[start, end]` ，返回序列 `[start, end]` 生成的所有可行的二叉搜索树。按照上文的思路，我们考虑枚举 `[start, end]` 中的值 iii 为当前二叉搜索树的根，那么序列划分为了 `[start, i - 1]` 和 `[i + 1, end]` 两部分。我们递归调用这两部分，即 `generateTrees(start, i - 1)` 和 `generateTrees(i + 1, end)` ，获得所有可行的左子树和可行的右子树，那么最后一步我们只要从可行左子树集合中选一棵，再从可行右子树集合中选一棵拼接到根节点上，并将生成的二叉搜索树放入答案数组即可。递归的入口即为 `generateTrees(1, n)` ，出口为当 `start > end` 的时候，当前二叉搜索树为空，返回空节点即可。

```java
import java.util.*;

// 普通二叉树节点
class TreeNode {

    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {
    }

    TreeNode(int val) {
        this.val = val;
    }

    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }

}

class Solution {

    public List<TreeNode> generateTrees(int n) {
        if (n == 0) {
            return new ArrayList<>();
        }
        return generateTrees(1, n);
    }

    public List<TreeNode> generateTrees(int start, int end) {
        List<TreeNode> allTrees = new ArrayList<>();
        if (start > end) {
            allTrees.add(null);
            return allTrees;
        }

        // 枚举可行根节点
        for (int i = start; i <= end; i++) {
            // 获得所有可行的左子树集合
            List<TreeNode> leftTrees = generateTrees(start, i - 1);

            // 获得所有可行的右子树集合
            List<TreeNode> rightTrees = generateTrees(i + 1, end);

            // 从左子树集合中选出一棵左子树，从右子树集合中选出一棵右子树，拼接到根节点上
            for (TreeNode left : leftTrees) {
                for (TreeNode right : rightTrees) {
                    TreeNode currTree = new TreeNode(i);
                    currTree.left = left;
                    currTree.right = right;
                    allTrees.add(currTree);
                }
            }
        }
        return allTrees;
    }

}
```

> - **一些题目的卡特兰数思路**

![image](./images/出栈序列.png)
![image](./images/括号匹配.png)
![image](./images/构造满二叉树.png)
![image](./images/电影购票.png)
