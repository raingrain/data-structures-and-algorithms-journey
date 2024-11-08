# 找到第 k 位数字

## [400. 第 N 位数字](https://leetcode.cn/problems/nth-digit/description/)

## [LCR 163. 找到第 k 位数字](https://leetcode.cn/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

> - **Question**
>   - 给你一个整数 `n` ，请你在无限的整数序列 `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...]` 中找出并返回第 `n` 位上的数字。
>   - **Tips**
>     - `1 <= n <= 2^31 - 1`

## Java

> - **模拟**

```java
// 我们还是需要先对n进行第一阶段的试减（更新n），得到目标数字所在的数值的长度len是多少。
// 然后根据len我们可以算得起点值s=10^len（例如len=2时，s=10；len=3时，s=100）。
// 由于每个数长度都是len，因此我们可以用剩余的数n除len，得到从起点的偏移量是多少（并将偏移量累加更新到s），然后对n做第二阶段的试减，减去的值就是⌊nlen⌋∗len。
// 如果试减后结果恰好为0，那么答案为当前s的最后一个数字；否则（试减结果大于0），则是x+1中（十进制表示，从左往右数）的第n个数字。
// 时间复杂度：O(log n)
// 空间复杂度：O(1)
class Solution {

    public int findKthNumber(int k) {
        return findNthDigit(k);
    }

    public int findNthDigit(int n) {
        int len = 1;
        while (len * 9 * Math.pow(10, len - 1) < n) {
            n -= (int) (len * 9 * Math.pow(10, len - 1));
            len++;
        }
        long s = (long) Math.pow(10, len - 1);
        s += n / len - 1;
        n -= len * (n / len);
        return n == 0 ? (int) (s % 10) : (int) ((s + 1) / Math.pow(10, len - n) % 10);
    }

}
```
