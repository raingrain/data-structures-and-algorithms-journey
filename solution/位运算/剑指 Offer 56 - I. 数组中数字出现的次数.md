# [剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

### 解题思路
1. 先解决其他数都出现偶数次的数组中找到出现奇数次的数（只有一个），由异或运算的结果与顺序无关和一个数与自己异或的结果为0，一个数与0异或结果为本身可得出将0与循环异或数组中的每一个数，最后出现偶数次的数都会被消去，只剩下那个出现奇数次的数
2. 后续还在想……

### 代码

```java
// java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int eor = 0, eorHasOne = 0;
        for (int temp : nums){
            eor ^= temp; 
        }
        int rightOne = eor & (~eor + 1);
        for (int temp : nums){
            if ((temp & rightOne) != 0){
                eorHasOne ^= temp;
            }
        }
        return new int[] {eorHasOne, eor ^ eorHasOne};
    }
}
```
