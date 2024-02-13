# k进制下1到n整数中1出现的次数

## [233. 数字 1 的个数](https://leetcode.cn/problems/number-of-digit-one/)

## [剑指 Offer 43. 1～n 整数中 1 出现的次数](https://leetcode.cn/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

> - ***Question 1***
>   - 输入一个整数 `n` ，求 `1 ~ n` 这 `n` 个整数的十进制表示中1出现的次数。
>   - 例如，输入 `12` ， `1 ~ 12` 这些整数中包含 `1` 的数字有 `1, 10, 11, 12` ， `1` 一共出现了 `5` 次。
>   - ***tips:***
>     - `1 <= n < 2^31`

## [3. k进制下一的个数](https://www.nowcoder.com/exam/test/69079544/detail?pid=33701596)

> - ***Question 2***
>   - 定义 `F(m, k)` 为 `k` 进制下 `1 ~ m` 中 `1` 出现的次数。给定 `k` 和 `n` ，求使得 `F(m, k) >= n` 最小的 `m` 是多少。

## [1067. 范围内的数字计数](https://leetcode.cn/problems/digit-count-in-range/)

> - ***Question 3***
>   - 给定一个在 `0 ~ 9` 之间的整数 `d` ，两个正整数 `low` 和 `high` 分别作为上下界，返回 `d` 在 `low` 和 `high` 之间的整数中出现的次数，包括 `low` 和 `high` 。

## [P2602 [ZJOI2010] 数字计数](https://www.luogu.com.cn/problem/P2602)

> - ***Question 4***
>   - 给定两个正整数 `a` 和 `b` ，求在 `[a, b]` 中的所有整数中，每个数码 `d` 各出现了多少次。
>   - ***输入描述***
>     - 仅包含一行两个整数 `a` 和 `b` ，含义如上所述。
>   - ***输出描述***
>     - 包含一行十个整数，分别表示 `0 ~ 9` 在 `[a, b]` 中出现了多少次。
>   - ***tips:***
>     - `1 <= a <= b <= 10^12`

---

## *Java*

> - ***Question 1: 数位DP***

```java
class Solution {
    
    public int countDigitOne(int num) {
        if (num < 1) {
            return 0;
        }
        // 获取数字长度
        int len = getLenOfNum(num);
        // 1 ~ (0,...,9)都只有1个1
        if (len == 1) {
            return 1;
        }
        // num 13625
        // temp 10000
        // num 7872328738273
        // temp 1000000000000
        int temp = (int) Math.pow(10, len - 1);
        // 第一位数字是几
        int first = num / temp;
        // 第一位上有几个1
        int firstOneNum = first == 1 ? num % temp + 1 : temp;
        // 剩下的位上有几个1
        int otherOneNum = first * (len - 1) * (temp / 10);
        // 如1364分为1~364和365~1364
        // 我们计算365到1364，分开计算每个数字能给个十百千位上贡献几个1
        // 对于千位为365
        // 对于百位，我们发现在十位和个位上0~9随意选择有10*10个
        // 对于十位，我们在百位和个位上随意选择有100
        // 个位在百位和十位上选择，也一样
        // 即选好后只有千位上只有一个选择能让这个数字在要求的范围内
        // 对于365到5364
        // 我们要手动计算这些的话
        // 分成365~1364，1365~2364，2365~3364，3365~4363，4365~5364这5个组，刚刚的结论每个组的结果都一样通项公式乘5即可
        // 后者的1就是firstOneNum + otherOneNum
        // 剩下的部分递归去算
        return firstOneNum + otherOneNum + countDigitOne(num % temp);
    }
    
    private int getLenOfNum(int num) {
        int len = 0;
        while (num != 0) {
            len++;
            num /= 10;
        }
        return len;
    }
    
}
```

> - ***Question 2: 数位DP***

```java
public class Solution {
    
    public static long minM(int n, int k) {
        int len = bits(n, k);
        long l = 1;
        long r = power(k, len + 1);
        long ans = r;
        while (l <= r) {
            long m = l + ((r - l) >> 1);
            if (ones(m, k) >= n) {
                ans = m;
                r = m - 1;
            } else {
                l = m + 1;
            }
        }
        return ans;
    }
    
    public static int bits(long num, int k) {
        int len = 0;
        while (num != 0) {
            len++;
            num /= k;
        }
        return len;
    }
    
    public static long power(long base, int power) {
        long ans = 1;
        while (power != 0) {
            if ((power & 1) != 0) {
                ans *= base;
            }
            base *= base;
            power >>= 1;
        }
        return ans;
    }
    
    public static long ones(long num, int k) {
        int len = bits(num, k);
        if (len <= 1) {
            return len;
        }
        long offset = power(k, len - 1);
        long first = num / offset;
        long curOne = first == 1 ? (num % offset) + 1 : offset;
        long restOne = first * (len - 1) * (offset / k);
        return curOne + restOne + ones(num % offset, k);
    }
    
}
```

> - ***Question 1 & Question3: 数位DP***

```java
class Solution {

    // Question 1
    public static int countDigitOne(int n) {
        return count(n, 1);
    }

    // Question 3
    // 1-b上的减去1-a-1上的就是a到b上的
    public static int digitsCount(int d, int a, int b) {
        return count(b, d) - count(a - 1, d);
    }

    // 统计1~num范围上所有的数中，数码d出现了多少次
    // 注意是1~num范围，不是0~num范围
    public static int count(int num, int d) {
        int ans = 0;
        // left : 当前位左边的情况数
        // right : 当前位右边的情况数
        // 当前位的数字是cur
        for (int right = 1, tmp = num, left, cur; tmp != 0; right *= 10, tmp /= 10) {
            // 情况1：
            // d != 0
            // 1 ~ 30583 , d = 5
            // cur < d的情况
            // 个位cur=3 : 0000~3057 5
            // 个位上没有额外加
            //
            // cur > d的情况
            // 十位cur=8 : 000~304 5 0~9
            // 十位上额外加 : 305 5 0~9
            //
            // cur == d的情况
            // 百位cur=5 : 00~29 5 00~99
            // 百位上额外加 : 30 5 00~83
            // ...
            // 情况2：
            // d == 0
            // 1 ~ 30583 d = 0
            // cur > d的情况
            // 个位cur=3 : 0001~3057 0
            // 个位上额外加 : 3058 0
            //
            // cur > d的情况
            // 十位cur=8 : 001~304 0 0~9
            // 十位上额外加 : 305 0 0~9
            //
            // cur > d的情况
            // 百位cur=5 : 01~29 0 00~99
            // 百位上额外加 : 30 0 00~99
            //
            // cur == d的情况
            // 千位cur=0 : 1~2 0 000~099
            // 千位上额外加 : 3 0 000~583
            // left是左边有多少种可能
            left = tmp / 10;
            cur = tmp % 10;
            // 如果当前为0，前缀不能为0
            if (d == 0) {
                left--;
            }
            // 先计算左边小于的情况
            ans += left * right;
            // 再算左边等于原数前缀
            if (cur > d) {
                // 36 5 24
                //    4
                // 36 4 00-99
                ans += right;
            } else if (cur == d) {
                // 36 5 24
                //    5
                // 36 5 00-24 (= 100 % 24 + 1)
                ans += num % right + 1;
            }
        }
        return ans;
    }

}
```

> - ***Question 4: 同Question3***

```java
import java.io.*;

public class Main {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            long a = (long) in.nval;
            in.nextToken();
            long b = (long) in.nval;
            for (int i = 0; i < 9; i++) {
                out.print(digitsCount(i, a, b) + " ");
            }
            out.println(digitsCount(9, a, b));
        }
        out.flush();
        out.close();
        br.close();
    }

    public static long digitsCount(int d, long a, long b) {
        return count(b, d) - count(a - 1, d);
    }

    public static long count(long num, int d) {
        long ans = 0;
        for (long right = 1, tmp = num, left, cur; tmp != 0; right *= 10, tmp /= 10) {
            left = tmp / 10;
            if (d == 0) {
                left--;
            }
            ans += left * right;
            cur = tmp % 10;
            if (cur > d) {
                ans += right;
            } else if (cur == d) {
                ans += num % right + 1;
            }
        }
        return ans;
    }

}
```

---

> ***last change: 2024/1/17***

---
