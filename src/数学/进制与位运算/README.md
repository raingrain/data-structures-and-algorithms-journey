# 常用位运算模板

## [2235. 两整数相加](https://leetcode.cn/problems/add-two-integers/)

## [371. 两整数之和](https://leetcode.cn/problems/sum-of-two-integers/)

## [剑指 Offer 65. 不用加减乘除做加法](https://leetcode.cn/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

## [面试题 17.01. 不用加号的加法](https://leetcode.cn/problems/add-without-plus-lcci/)

> - ***Question 1***
>   - 写一个函数，求两个整数之和，要求在函数体内不得使用 `+, -, *, /, %` 等运算符号。
>   - ***tips:***
>     - `a, b` 均可能是负数或 `0`
>     - 结果不会溢出 `32` 位整数

## [29. 两数相除](https://leetcode.cn/problems/divide-two-integers/)

## [剑指 Offer II 001. 整数除法](https://leetcode.cn/problems/xoh6Oh/)

> - ***Question 2***
>   - 写一个函数，求两个整数之商（小数向下取整），要求在函数体内不得使用 `+, -, *, /, %` 等运算符号。
>   - ***tips:***
>     - 被除数和除数均为 `32` 位有符号整数，其数值范围是 `[−2^31, 23^1 − 1]`
>     - 除数不为 `0`
>     - 如果除法结果溢出，则返回 `23^1 − 1`

## [2166. 设计位集](https://leetcode.cn/problems/design-bitset/)

> - ***Question 3***
>   - 位集 `Bitset` 是一种能以紧凑形式存储位的数据结构。请你实现 `Bitset` 类：
>     - `Bitset(int size)` 用 `size` 个位初始化 `Bitset` ，所有位都是 `0` 。
>     - `void fix(int idx)` 将下标为 `idx` 的位上的值更新为 `1` 。如果值已经是 `1` ，则不会发生任何改变。
>     - `void unfix(int idx)` 将下标为 `idx` 的位上的值更新为 `0` 。如果值已经是 `0` ，则不会发生任何改变。
>     - `void flip()` 翻转 `Bitset` 中每一位上的值。换句话说，所有值为 `0` 的位将会变成 `1` ，反之亦然。
>     - `boolean all()` 检查 `Bitset` 中每一位的值是否都是 `1` 。如果满足此条件，返回 `true` ；否则，返回 `false` 。
>     - `boolean one()` 检查 `Bitset` 中是否至少一位的值是 `1` 。如果满足此条件，返回 `true` ；否则，返回 `false` 。
>     - `int count()` 返回 `Bitset` 中值为 1 的位的 总数 。
>     - `String toString()` 返回 `Bitset` 的当前组成情况。注意，在结果字符串中，第 `i` 个下标处的字符应该与 `Bitset` 中的第 `i` 位一致。
>   - ***tips:***
>     - `1 <= size <= 10^5`
>     - `0 <= idx <= size - 1`
>     - 至多调用所有方法总共 `10^5` 次
>     - 至少调用 `all, one, count, toString` 方法一次
>     - 至多调用 `toString` 方法 `5` 次

## 左老师课堂讲述

> - ***Question 4***
>   - 给定一个正数 `x` ，已知用 `m` 个二进制位一定能表示 `x` ，从高位到低位打印 `x` 每一位的状态
> - ***Question 5***
>   - 给定一个正数 `x` ，打印 `<=x` 最大的 `2` 的幂，到底是 `2` 的几次方。

## Java

> - ***得到状态s第j位的状态***

```java
(s >> j) & 1
```

> - ***把状态s第j位的状态设置成1且其它位置状态不变***

```java
s = s | (1 << j)
```

> - ***把状态s第j位的状态设置成0且其它位置状态不变***

```java
s = s & (~(1 << j))
```

> - ***打印二进制***

```java
class Solution {
    
    private static void print(int num) {
        for (int i = 31; i >= 0; --i) {
            System.out.print((num & (1 << i)) == 0 ? 0 : 1);
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        int num = 30;
        print(num);
    }
    
}
```

> - ***获得相反数***

```java
class Solution {
    
    private static int getOppositeNumber(int num) {
        return ~num + 1;
    }
    
    public static void main(String[] args) {
        int num = 30;
        System.out.println(-num);
        System.out.println(getOppositeNumber(num));
    }
    
}
```

> - ***位图***

```java
class Solution {
    
    public static void main(String[] args) {
        BitMap bitMap = new BitMap(300);
        bitMap.add(233);
        System.out.println(bitMap.contains(233));
        bitMap.delete(233);
        System.out.println(bitMap.contains(233));
    }
    
}

class BitMap {
    
    // 位图整体从左往右存储，但一个long内部是从右往左存储的
    private final long[] bits;
    
    public BitMap(int max) {
        // (max + 64) >> 6 = (max + 64) / 6
        // 上述式子用于计算整个数组需要几个long类型的数
        // 比如max为63或者0，(max + 64) / 6 = 1
        // max为64时，(max + 64) / 6 = 2
        this.bits = new long[(max + 64) >> 6];
    }
    
    // 假定num在某一个long中
    // 获取num存储在long的二进制的哪一位上，返回那一位标为1，其他位全为0的一个数字
    private long getIndex(int num) {
        // 注意是1L是64位而1是32位
        // num & 63 = num % 63用于获取位置
        // 1左移到指定位置上即可获得要求的数字
        return 1L << (num & 63);
    }
    
    // 添加一个数字到集合中
    public void add(int num) {
        // bits[num >> 6]为num所在的long类型的数字
        // 或保证了其他位置不变，因为任何数或0都是它本身
        // 要加入的位置不管有没有都或上1变1
        bits[num >> 6] |= getIndex(num);
    }
    
    // 删除集合中的一个数
    public void delete(int num) {
        // 与保证了其他位置不变，因为任何数与1都是它本身
        // 要删除的位置不管有没有都与上0变0
        bits[num >> 6] &= ~getIndex(num);
    }
    
    // 集合中有没有这个数字
    public boolean contains(int num) {
        // 两个数都是1相与为1
        // 其他数与0全变0
        // 那个位置不为1集合中才存在这个数字
        return (bits[num >> 6] & getIndex(num)) != 0;
    }

    // 集合中没有这个数字就加上，有就删除
    public void reverse(int num) {
        bits[num >> 6] ^= 1 << getIndex(num);
    }
    
}
```

> - ***纯位运算实现加减乘除***

```java
class Solution {
    
    public int getSum(int a, int b) {
        return add(a, b);
    }
    
    public int sum(int a, int b) {
        return add(a, b);
    }
    
    // 加法
    public int add(int a, int b) {
        // b等于0，sum就是a
        int sum = a;
        // 加法就是无进位相加加上进位信息
        // 但是因为不给用加法，我们只能迭代下去，直到进位信息为0
        while (b != 0) {
            // a ^ b得到无进位相加信息
            sum = a ^ b;
            // a & b得到哪个相加时哪个位置产生进位，左移一位表示进位加到下一位
            b = (a & b) << 1;
            a = sum;
        }
        return sum;
    }
    
    // 获得相反数
    private int getOppositeNumber(int num) {
        return add(~num, 1);
    }
    
    // 减法
    public int minus(int a, int b) {
        // 加上相反数
        return add(a, getOppositeNumber(b));
    }
    
    // 乘法
    public int multiply(int a, int b) {
        int ans = 0;
        // 有点像矩阵快速幂
        while (b != 0) {
            if ((b & 1) != 0) {
                ans = add(ans, a);
            }
            a <<= 1;
            // 不带符号右移，不然负数会死循环
            b >>>= 1;
        }
        return ans;
    }
    
    // 判断一个数是不是负数
    private boolean isNegative(int num) {
        return num < 0;
    }
    
    // 在-Integer.MAX_VALUE和Integer.MAX_VALUE之间进行的除法
    private int div(int a, int b) {
        // 全部转成正数
        int x = isNegative(a) ? getOppositeNumber(a) : a;
        int y = isNegative(b) ? getOppositeNumber(b) : b;
        int ans = 0;
        for (int i = 30; i >= 0; i = minus(i, 1)) {
            // 避免y左移影响到符号位而变成负数，求两者最接近，且x>=y
            if ((x >> i) >= y) {
                // a/b=c，把c变成2进制多项式相乘转10进制的格式，求每个二进制多项式中的项的2的幂，对应结果上的同位置变1
                ans |= (1 << i);
                // x-最接近的y，即每次减去多项式中的最高幂项
                x = minus(x, y << i);
            }
        }
        // 异号加负号
        // 同号返回正数结果
        // 不等可以用逻辑异或代替
        return isNegative(a) ^ isNegative(b) ? getOppositeNumber(ans) : ans;
    }
    
    // 除法
    // a / b
    public int divide(int a, int b) {
        // 系统最小值的绝对值比系统最大值大一
        if (a == Integer.MIN_VALUE && b == Integer.MIN_VALUE) {
            // 自己除自己
            return 1;
        } else if (b == Integer.MIN_VALUE) {
            // 没人比它大
            return 0;
        } else if (a == Integer.MIN_VALUE) {
            if (b == getOppositeNumber(1)) {
                // 特殊
                return Integer.MAX_VALUE;
            } else {
                // 最小值加一再除，e是为了防止加1除和最小值除的结果由于这个1而导致的结果的变小，d/b用来判断差值是否小于除数，小于即结果+0，大于即结果加1
                // a / b
                // (a + 1) / b
                int ans = div(add(a, 1), b);
                // 求没算的长度 a - b * c = d
                // 看看这部分长度够不够b d / b = e
                // 够就加上，不够为0也不影响
                // ans = c + e
                return add(ans, div(minus(a, multiply(ans, b)), b));
            }
        } else {
            // 正常情况
            return div(a, b);
        }
    }
    
}
```

> - ***位集***

```java
class Bitset {

    // size int 32
    // size / 32 向上取整
    private final int[] bits;
    private final int size;
    // 0的数量
    private int zeros;
    // 1的数量
    private int ones;
    // 有没有反转过
    private boolean reverse;

    public Bitset(int size) {
        bits = new int[(size + 31) / 32];
        this.size = size;
        zeros = size;
        ones = 0;
        reverse = false;
    }

    // 把idx位置的状态，如果是0，变成1；如果是1，没有变化！
    public void fix(int idx) {
        // 第几位数字
        int index = idx / 32;
        // 这个数字上的第几位
        int bit = idx % 32;
        if (!reverse) {
            // 没反转过，是0就直接变1
            if ((bits[index] & (1 << bit)) == 0) {
                // 01数量变化
                zeros--;
                ones++;
                // 1或到这个位上
                bits[index] |= (1 << bit);
            }
        } else {
            // 反转过，但不变数组，你是1代表你反转后是0
            if ((bits[index] & (1 << bit)) != 0) {
                // 数量变化
                zeros--;
                ones++;
                // 1异或1成0代表反转后是1
                bits[index] ^= (1 << bit);
            }
        }
    }

    // 同fix
    public void unfix(int idx) {
        int index = idx / 32;
        int bit = idx % 32;
        if (!reverse) {
            if ((bits[index] & (1 << bit)) != 0) {
                ones--;
                zeros++;
                bits[index] ^= (1 << bit);
            }
        } else {
            if ((bits[index] & (1 << bit)) == 0) {
                ones--;
                zeros++;
                bits[index] |= (1 << bit);
            }
        }
    }

    // 0变1，1变0
    // 假翻转，只改变信息，不改变实际位置
    public void flip() {
        reverse = !reverse;
        int tmp = zeros;
        zeros = ones;
        ones = tmp;
    }

    // 是不是所有的位都是1
    public boolean all() {
        return ones == size;
    }

    // 是不是至少有1位是1
    public boolean one() {
        return ones > 0;
    }

    // 返回1的数量！
    public int count() {
        return ones;
    }

    // 转成字符串
    public String toString() {
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < size; i++) {
            // 从左往右转，题目要求的索引类似于数组
            int status = bits[i / 32] & (1 << (i % 32));
            // 反转过的输出时0变1，1变0，没反转过的正常输出
            builder.append(reverse ? (status == 0 ? '1' : '0') : (status == 0 ? '0' : '1'));
        }
        return builder.toString();
    }

}
```

> - ***Question 4 & Question 5***

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    // 给定一个正数x
    // 已知用m个二进制位一定能表示x
    // 从高位到低位打印x每一位的状态
    public static void show1(int x, int m) {
        for (int p = m - 1, t = x; p >= 0; p--) {
            if (1 << p <= t) {
                t -= 1 << p;
                System.out.println(x + "的第" + p + "位是1");
            } else {
                System.out.println(x + "的第" + p + "位是0");
            }
        }
    }

    // 给定一个正数x
    // 打印<=x最大的2的幂
    // 到底是2的几次方
    public static void show2(int x) {
        int power = 0;

        // 以下注释掉的写法不对，没有考虑溢出
        // while ((1 << power) <= x) {
        //  power++;
        // }
        // power--;

        // 防止溢出的写法
        while ((1 << power) <= (x >> 1)) {
            power++;
        }
        System.out.println("<=" + x + "最大的2的幂，是2的" + power + "次方");
    }

    public static void main(String[] args) {
        // 需要保证用m个二进制位一定能表示x
        int x = 101;
        int m = 10;
        show1(x, m);

        x = 13;
        show2(x);
        x = 16;
        show2(x);
        x = 2000000000;
        show2(x);
    }

}
```
