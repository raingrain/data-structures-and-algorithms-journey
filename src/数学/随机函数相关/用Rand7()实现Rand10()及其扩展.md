# 用Rand7()实现Rand10()及其扩展

## [470. 用 Rand7() 实现 Rand10()](https://leetcode.cn/problems/implement-rand10-using-rand7/)

## ## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第9章 其他题目 从5随机到7随机及其扩展

> - ***Question 1***
>   - 给定方法 `rand7` 可生成 `[1,7]` 范围内的均匀随机整数，试写一个方法 `rand10` 生成 `[1,10]` 范围内的均匀随机整数。
>   - 你只能调用 `rand7()` 且不能调用其他方法。请不要使用系统的 `Math.random()` 方法。
>   - 每个测试用例将有一个内部参数 `n` ，即你实现的函数 `rand10()` 在测试时将被调用的次数。请注意这不是传递给 `rand10()` 的参数。
> - ***Question 2***
>   - 给定一个以 `p` 概率产生 `0` ，以 `1-p` 概率产生 `1` 的随机函数rand01p，实现一个 `01` 等概率发生器。

---

## *Java*

> - ***概率论***
>   - 对于生成 `a-b` 之间等概率随机的 `f` 函数，我们可以直接将 `a-b` 分成两半， `do while` 调用 `f` ，小于一半的为 `0` ，大于一半的为 `1` ，等于中间值（奇数）重做
>   - 把 `c~d` 变成 `0~(d-c)` ，然后看 `d-c` 在哪个二进制范围上，生成 `0` 到范围上的等概率随机，在把生成的数加上 `d-c` 就是 `c~d` 范围上的随机数了。
>   - 核心在于01等概率发生器的使用和利用二进制实现随机

```java
/**
 * The rand7() API is already defined in the parent class SolBase.
 * public int rand7();
 * @return a random integer in the range 1 to 7
 */
class Solution extends SolBase {

    // 01等概率随机
    public int rand01() {
        int ans = 0;
        do {
            ans = rand7();
        } while (ans == 4);
        return ans < 4 ? 0 : 1;
    }

    // 0-15随机生成一个
    public int rand015() {
        return ((rand01() << 3) + (rand01() << 2) + (rand01() << 1) + rand01());
    }

    public int rand10() {
        int ans = 0;
        // 只需要0-9的数字
        do {
            ans = rand015();
        } while (ans > 9);
        return ans + 1;
    }

    // 以p的概率生成0，1-p的概率生成1
    public int rand01P() {
        double p = 0.83;
        return Math.random() < p ? 0 : 1;
    }
    
    // 利用概率论的结论直接得出就可以了
    public int rand01ByRand01P() {
        int x;
        do {
            x = rand01P();
        } while (x == rand01P());
        // x 0 1
        // x 1 0
        // 这两种情况的概率相等，取第一个值作为那种情况的对应值
        return x;
    }

}
```
