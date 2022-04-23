# [剑指 Offer 56 - II. 数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

### 解题思路
1. 如果要求时间复杂度为O(n),额外空间复杂度为O(1)的话就用位运算，不然哈希表也可以
2. 我们设置一个长为32的数组arr，因为java中int为32位整型，我们遍历nums中的每一个数，把每一个数字变成32位二进制数组存储的形式，并将这个数组加到arr中
3. 我们知道，nums中的数，分为出现m次和出现k次，出现m次的数中，其在某个位置上有可能是0也可能是1，但是1的数加起来必然导致arr中对应位置的结果是7的倍数，那么假如你要找的数ans对应位置数是0，那么显然arr中对应结果就是7的倍数，如果是1，那么对应位置的结果吗模7就会等于k，k<m。所以遍历arr，如果arr某个位置上的数模m等于0，那么ans在这一位置上等于0，否则等于1。
4. 赋值采用或来赋值，与0或不改变结果，与1或等于1，我们从右往左把1或给ans，要想知道哪一位要或就左移1即可，这样就找到了只出现k次的数字

### 代码

```java
// java位运算
class Solution {
    public int singleNumber(int[] nums) {
        int[] arr = new int[32];
        for (int num : nums) {
            for (int i = 0; i < 32; i++) {
                // num >> i 可以把i位置放到最后一位，与1判断第i位是0还是1，最后直接加到arr[i]上，这样也不用判断1加0不加了反正加了0也没用
                arr[i] += ((num >> i) & 1);
            }
        }
        int ans = 0;
        for (int i = 0;i < 32; i++) {
            if (arr[i] % 3 != 0) {
                ans |= (1 << i);
            }
        }
        return ans;
    }
}
```

```java
// java哈希表
class Solution {
    public int singleNumber(int[] nums) {
        int tmp = 0;
        // 一个词频统计表
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            if (map.containsKey(num)) {
                // 有就词频加1
                map.put(num, map.get(num)+ 1);
            } else {
                // 没有就往表里面加词
                map.put(num, 1);
            }
        }
        for (int num : map.keySet()) {
            // 有没有等于词频等于k的词
            if (map.get(num) == 1) {
                tmp = num;
            }
        }
        return tmp;
    }
}
```

```java
// 假如是必定有一些数字出现了m次，而剩下的那个数字未必出现了k次，如果出现的不是k次就返回-1，不然返回它本身
class Solution {
    public int singleNumber(int[] nums) {
        int[] arr = new int[32];
        for (int num : nums) {
            for (int i = 0; i < 32; i++) {
                // num >> i 可以把i位置放到最后一位，与1判断第i位是0还是1，最后直接加到arr[i]上，这样也不用判断1加0不加了反正加了0也没用
                arr[i] += ((num >> i) & 1);
            }
        }
        int ans = 0;
        for (int i = 0;i < 32; i++) {
            // 有个bug，如果这个出现了k次的数字是0，那么以下逻辑就无法判断正误，需要在后面在循环看看0是不是出现k次的那个
            if (arr[i] % m == 0) {
                continue;
            } else if (arr[i] % m == k) {
                ans |= (1 << i);
            } else {
                return -1; 
            }
        }
        // 如果是0的话上面的代码会一直执行第一步，ans会一直等于0
        if (ans == 0) {
            int count = 0;
            for (int num : arr) {
                if (num == 0) {
                    count++;
                }
            }
            if (count != k) {
                return -1;
            }
        }
        return ans;
    }
}
```
