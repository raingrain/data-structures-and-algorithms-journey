# 有多少种子序列的字符种数等于k种

## 百度

> - ***Question***
>   - 给定一个字符串 `str` ，和一个正数 `k` 。
>   - `str` 子序列的字符种数必须是 `k` 种，返回有多少子序列满足这个条件。
>   - ***tips:***
>     - `str` 中全是小写字母

---

## *Java*

> - ***暴力递归***

```java
class Solution {
    
    public static int ways(String s, int k) {
        char[] str = s.toCharArray();
        // 搞出词频
        int[] wordFrequency = new int[26];
        for (char c : str) {
            wordFrequency[c - 'a']++;
        }
        return recursion(wordFrequency, 0, k);
    }
    
    // 在词频表index及其往后的位置上选择字符，返回凑够rest种所需的方法数
    private static int recursion(int[] wordFrequency, int index, int rest) {
        if (index == wordFrequency.length) {
            // 没有词可以选了！必须不需要再选择词才行
            return rest == 0 ? 1 : 0;
        }
        // 还有词可以选
        // 不选index位置的字符，还要选的种类不变
        int p1 = recursion(wordFrequency, index + 1, rest);
        // 选index位置的字符，还要选的种类减1
        // 但注意，假设index位置有n个字符，对于子序列来说，可以从n中选1个，2个，直至n个，即组合数之和然后乘以下一级递归
        // 如果不需要再选了，直接给0
        int p2 = rest > 0 ? recursion(wordFrequency, index + 1, rest - 1) * cn1AddToCnn(wordFrequency[index]) : 0;
        // 返回所有情况
        return p1 + p2;
    }
    
    // C(n, 1) + ... + C(n, n) = 2 ^ n - 1;
    private static int cn1AddToCnn(int n) {
        return (1 << n) - 1;
    }
    
    public static void main(String[] args) {
        System.out.println(ways("acbbca", 3));
    }
    
}
```
