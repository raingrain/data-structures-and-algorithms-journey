# RMQ

## RMQ代码模板

> - ***Question***
>   - 给定一个数组，想随时查询任何范围上的最大值。
>   - 这个数组在给定后不会修改，但查询会有很多次。
>   - 请你实现一个查询极快的结构。

---

## *Java*

> - ***动态规划实现查询表***
>   - 生成RMQ的查询结构的时间复杂度为 `O(n*logn)` ，后续查询一次的时间复杂度为 `O(1)` 。

```java
class Solution {

    private static class RMQ {

        private final int[][] dp;

        public RMQ(int[] arr) {
            // dp[i][j]表示从arr[i - 1]位置开始往右数2的j次方个数内最大值是多少，越界范围就认为到右边界
            dp = new int[arr.length + 1][log2ToInt(arr.length) + 1];
            // 0位置弃而不用，RMQ与线段树一样从1开始
            for (int i = 1; i <= arr.length; i++) {
                // i 0：从下标i开始，往下连续的2的0次方个数，中，最大值
                // 只扩张一个数字，选最大值
                dp[i][0] = arr[i - 1];
            }
            // 从左往右，自顶向下
            for (int j = 1; (1 << j) <= arr.length; j++) {
                // i...连续的、2的1次方个数，这个范围，最大值
                // i...连续的、2的2次方个数，这个范围，最大值
                // i...连续的、2的3次方个数，这个范围，最大值
                for (int i = 1; i + (1 << j) - 1 <= arr.length; i++) {
                    // dp[10][3]
                    // 下标10开始，连续的8个数，最大值是多少
                    // 划分为10开始连续的4个数和14开始连续的4个数
                    // 1) dp[10][2]
                    // 2) dp[14][2]
                    // i + (1 << (j - 1))就是下一个一半的开始
                    // j - 1是当前次方/2
                    dp[i][j] = Math.max(dp[i][j - 1], dp[i + (1 << (j - 1))][j - 1]);
                }
            }
        }

        public int max(int left, int right) {
            // 任何一个范围都有其长度
            // 找出距离这个长度最近的2的某次方，这个次方值为k
            // 那么这个长度可以划分为重叠的两部分
            // 从最左往右延申k和最右往左延申k（最好情况是两个各占一半）
            // 两个范围的最大值在dp中查出来再比较就是整个范围上的最大值
            int k = log2ToInt(right - left + 1);
            // right - (1 << k) + 1就是右边范围的左起始点
            return Math.max(dp[left][k], dp[right - (1 << k) + 1][k]);
        }

        // 返回距离一个数字num最接近的2的k次方的这个k
        private int log2ToInt(int num) {
            int ans = 0;
            while ((1 << ans) <= (num >> 1)) {
                ans++;
            }
            return ans;
            // 可以直接用数学公式
            // return (int) (Math.log10(num) / Math.log10(2));
        }

    }

    // 为了测试
    private static class Right {

        private final int[][] max;

        public Right(int[] arr) {
            int n = arr.length;
            max = new int[n + 1][n + 1];
            for (int l = 1; l <= n; l++) {
                max[l][l] = arr[l - 1];
                for (int r = l + 1; r <= n; r++) {
                    max[l][r] = Math.max(max[l][r - 1], arr[r - 1]);
                }
            }
        }

        public int max(int l, int r) {
            return max[l][r];
        }

    }

    // 为了测试
    public static int[] randomArray(int n, int v) {
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = (int) (Math.random() * v);
        }
        return arr;
    }

    // 为了测试
    public static void main(String[] args) {
        int N = 150;
        int V = 200;
        int testTimeOut = 20000;
        int testTimeIn = 200;
        System.out.println("测试开始");
        for (int i = 0; i < testTimeOut; i++) {
            int n = (int) (Math.random() * N) + 1;
            int[] arr = randomArray(n, V);
            int m = arr.length;
            RMQ rmq = new RMQ(arr);
            Right right = new Right(arr);
            for (int j = 0; j < testTimeIn; j++) {
                int a = (int) (Math.random() * m) + 1;
                int b = (int) (Math.random() * m) + 1;
                int l = Math.min(a, b);
                int r = Math.max(a, b);
                int ans1 = rmq.max(l, r);
                int ans2 = right.max(l, r);
                if (ans1 != ans2) {
                    System.out.println("出错了!");
                }
            }
        }
        System.out.println("测试结束");
    }

}
```

---

> ***last change: 2023/5/29***

---
