# 中国剩余定理及其拓展

![image](./images/中国剩余定理.png)

![image](./images/拓展中国剩余定理.png)

## [P1495 【模板】中国剩余定理（CRT）/ 曹冲养猪](https://www.luogu.com.cn/problem/P1495)

> - ***Question 1***
>   - 自从曹冲搞定了大象以后，曹操就开始捉摸让儿子干些事业，于是派他到中原养猪场养猪，可是曹冲满不高兴，于是在工作中马马虎虎，有一次曹操想知道母猪的数量，于是曹冲想狠狠耍曹操一把。举个例子，假如有 `16` 头母猪，如果建了 `3` 个猪圈，剩下 `1` 头猪就没有地方安家了。如果建造了 `5` 个猪圈，但是仍然有 `1` 头猪没有地方去，然后如果建造了 `7` 个猪圈，还有 `2` 头没有地方去。你作为曹总的私人秘书理所当然要将准确的猪数报给曹总，你该怎么办？
>   - ***输入描述***
>     - 第一行包含一个整数 `n` 表示建立猪圈的次数，接下来 `n` 行，每行两个整数 `ai, bi` ，表示建立了 `ai` 个猪圈，有 `bi` 头猪没有去处。你可以假定 `a1 ~ an` 互质。
>   - ***输出描述***
>     - 输出包含一个正整数，即为曹冲至少养母猪的数目。
>   - ***tips:***
>     - `1 <= n <= 10`
>     - `0 <= bi < ai <= 100000`
>     - `1 <= 所有ai的乘积 <= 10^18`

## [P4777 【模板】扩展中国剩余定理（EXCRT）](https://www.luogu.com.cn/problem/P4777)

> - ***Question 2***
>   - 给定 `n` 组非负整数 `ai, bi` ，求解关于 `x` 的方程组的最小非负整数解。一共 `n` 个同余方程 `x ≡ bi (mod ai)` 。
>   - ***输入描述***
>     - 输入第一行包含整数 `n` 。接下来 `n` 行，每行两个非负整数 `ai, bi` 。
>   - ***输出描述***
>     - 输出一行，为满足条件的最小非负整数 `x` 。
>   - ***tips:***
>     - `1 <= n <= 10^5`
>     - `1 <= bi, ai <= 10^12`

---

## *Java*

> - ***Question 1: 中国剩余定理模板***

```java
import java.io.*;

public class Main {

    public static int MAXN = 11;

    public static long m[] = new long[MAXN];

    public static long r[] = new long[MAXN];

    // 中国剩余定理模版
    public static long crt(int n) {
        long lcm = 1;
        for (int i = 1; i <= n; i++) {
            lcm = lcm * m[i];
        }
        long ai, ci, ans = 0;
        for (int i = 1; i <= n; i++) {
            // ai = lcm / m[i]
            ai = lcm / m[i];
            // ai逆元，在%m[i]意义下的逆元
            exgcd(ai, m[i]);
            // ci = (ri * ai * ai逆元) % lcm
            ci = multiply(r[i], multiply(ai, x, lcm), lcm);
            ans = (ans + ci) % lcm;
        }
        return ans;
    }

    // 讲解139 - 扩展欧几里得算法
    public static long d, x, y, px, py;

    public static void exgcd(long a, long b) {
        if (b == 0) {
            d = a;
            x = 1;
            y = 0;
        } else {
            exgcd(b, a % b);
            px = x;
            py = y;
            x = py;
            y = px - py * (a / b);
        }
    }

    // 讲解033 - 位运算实现乘法
    // a*b过程每一步都%mod，这么写是防止溢出，也叫龟速乘
    public static long multiply(long a, long b, long mod) {
        a = (a % mod + mod) % mod;
        b = (b % mod + mod) % mod;
        long ans = 0;
        while (b != 0) {
            if ((b & 1) != 0) {
                ans = (ans + a) % mod;
            }
            a = (a + a) % mod;
            b >>= 1;
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            m[i] = (long) in.nval;
            in.nextToken();
            r[i] = (long) in.nval;
        }
        out.println(crt(n));
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 1 & Question 2: 扩展中国剩余定理***

```java
import java.io.*;

public class Main {

    public static int MAXN = 100001;

    public static long m[] = new long[MAXN];

    public static long r[] = new long[MAXN];

    // 扩展中国剩余定理模版
    public static long excrt(int n) {
        long tail = 0, lcm = 1, tmp, b, c, x0;
        // ans = lcm * x + tail
        for (int i = 1; i <= n; i++) {
            // ans = m[i] * y + ri
            // lcm * x + m[i] * y = ri - tail
            // a = lcm
            // b = m[i]
            // c = ri - tail
            b = m[i];
            c = ((r[i] - tail) % b + b) % b;
            exgcd(lcm, b);
            if (c % d != 0) {
                return -1;
            }
            // ax + by = gcd(a,b)，特解是，x变量
            // ax + by = c，特解是，x变量 * (c/d)
            // ax + by = c，最小非负特解x0 = (x * (c/d)) % (b/d) 取非负余数
            // 通解 = x0 + (b/d) * n
            x0 = multiply(x, c / d, b / d);
            // ans = lcm * x + tail，带入通解
            // ans = lcm * (x0 + (b/d) * n) + tail
            // ans = lcm * (b/d) * n + lcm * x0 + tail
            // tail' = tail' % lcm'
            tmp = lcm * (b / d);
            tail = (tail + multiply(x0, lcm, tmp)) % tmp;
            lcm = tmp;
        }
        return tail;
    }

    // 讲解139 - 扩展欧几里得算法
    public static long d, x, y, px, py;

    public static void exgcd(long a, long b) {
        if (b == 0) {
            d = a;
            x = 1;
            y = 0;
        } else {
            exgcd(b, a % b);
            px = x;
            py = y;
            x = py;
            y = px - py * (a / b);
        }
    }

    // 讲解033 - 位运算实现乘法
    // a*b过程每一步都%mod，这么写是防止溢出，也叫龟速乘
    public static long multiply(long a, long b, long mod) {
        a = (a % mod + mod) % mod;
        b = (b % mod + mod) % mod;
        long ans = 0;
        while (b != 0) {
            if ((b & 1) != 0) {
                ans = (ans + a) % mod;
            }
            a = (a + a) % mod;
            b >>= 1;
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            m[i] = (long) in.nval;
            in.nextToken();
            r[i] = (long) in.nval;
        }
        out.println(excrt(n));
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/8/31***

---
