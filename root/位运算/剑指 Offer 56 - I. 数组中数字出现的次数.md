# [剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

### 解题思路
1. 先解决其他数都出现偶数次的数组中找到出现奇数次的数（只有一个），由异或运算的结果与顺序无关和一个数与自己异或的结果为0，一个数与0异或结果为本身可得出将0与循环异或数组中的每一个数，最后出现偶数次的数都会被消去，只剩下那个出现奇数次的数
2. 如果出现奇数次的数字有两个，那么最后eor就等于a^b，并且eor一定不等于0，因为a与b不可能相等，那么eor上一定有个最右侧的1（意味着a和b在该位置上不相同，一个是0，一个是1，因为异或是相同为0，不同为1），rightOne就是把这个1提取出来
3. 假设这个1在第k位，那么整个数组必然分成两种数，第k位为0或者为1，且a与b分别在不同的一组中，并且在这两组中，出现偶数次的数在自己组中还是出现了偶数次，出现了奇数次的数a和b在自己组中就是思路1中的情况，我只需要和第k位为1的数进行异或，必然能找到k位为1的组中的a或者b，然后再用eor异或eorHasOne，得到另外一个即可

### 代码

```java
// java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int eor = 0, eorHasOne = 0;
        for (int temp : nums){
            eor ^= temp; 
        }
        // eor = 00110010, rightOne = 00000010
        // 获得eor最右侧的1，一个数与上自己的相反数（取反+1），得到一个只有一个1，其余全为0的数，这个1是eor本身最右侧的1
        int rightOne = eor & (~eor + 1);
        for (int temp : nums){
            // 找到k位置上为1的数，因为任何数与0都是0，1与1才等于1，也就是把数组分成两组，重复上面那个循环罢了
            if ((temp & rightOne) != 0){
                eorHasOne ^= temp;
            }
        }
        return new int[] {eorHasOne, eor ^ eorHasOne};
    }
}
```
