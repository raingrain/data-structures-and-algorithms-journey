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

---

## *Java*

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

---

> ***last change: 11/14***

---
