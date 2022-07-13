### 题目

- [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/)
- [剑指 Offer 10- I. 斐波那契数列](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)

### 题意

![image](https://user-images.githubusercontent.com/75558694/178433513-ec9b57d5-05bb-41f3-86e9-c2e240e3033a.png)

---

### Java实现

#### *思路*

> - **动态规划法**
>   ---
>   - 暴力递归改记忆化搜索改动态规划后利用滚动数组压缩空间，与打表无异
>   
> ---
>
> - **通项公式法**
>   ---
>   ![image](https://user-images.githubusercontent.com/75558694/178434052-128eb8eb-e677-455b-848c-1e4a0ddc7998.png)
>   
> ---
>   
> - **矩阵快速幂法**
>   ---
>   ![image](https://user-images.githubusercontent.com/75558694/178434279-40c64c96-ee6f-46b4-813c-b11dd86a63cc.png)
>   - 这是简化版的快速幂公式，推广版参考[牛的数量]()。

#### *代码*

```java
class Solution {
    public int fib(int n) {
        if (n == 0) {
            return 0;
        } else if (n == 1 || n == 2) {
            return 1;
        } else if (n == 3) {
            return 2;
        } else {
            // return fibByDP(n);
            // return fibByformula(n);
            return fibByFastExponentiation(n);
        }
    }

    // 递归->动态规划->滚动数组压缩/打表的另外一种形式
    public int fibByDP(int n) {
        int p = 1;
        int q = 1;
        int r = 2;
        for (int i = 4; i <= n; i++) {
            p = q;
            q = r;
            r = (p + q) % 1000000007;
        }
        return r;
    }

    // 求根公式，后面精度不够了
    public int fibByformula(int n) {
        double sqrt5 = Math.sqrt(5);
        double fibN = Math.pow((1 + sqrt5) / 2, n) - Math.pow((1 - sqrt5) / 2, n);
        return (int) (Math.round(fibN / sqrt5) % 1000000007);
    }

    public int fibByFastExponentiation(int n) {
        int[][] base = {{1, 1}, {1, 0}};
        // 取决于你想把哪个作为基准矩阵
        // int[][] res = matrixPower(base, n - 1);
        // return res[0][0];
        int[][] res = matrixPower(base, n - 2);
        // 注意相加后还要再取一次模
        return (res[0][0] + res[0][1]) % 1000000007;
    }

    // 求矩阵matrix的power次幂
    public int[][] matrixPower(int[][] matrix, int power) {
        // 构建同型单位矩阵
        int[][] res = new int[matrix.length][matrix[0].length];
        for (int i = 0; i < matrix.length; i++) {
            res[i][i] = 1;
        }
        // 把指数变成二进制转换成加法，把幂运算换成多个较小幂运算的积
        for (; power > 0; power >>= 1) {
            // 二进制上这位为1代表它是因子
            if ((power & 1) == 1) {
                res = muliMatrix(res, matrix);
            }
            // 矩阵以平方级自增
            matrix = muliMatrix(matrix, matrix);
        }
        return res;
    }

    public int[][] muliMatrix(int[][] A, int[][] B) {
        int[][] res = new int[A.length][A[0].length];
        for (int i = 0; i < A.length; i++) {
            for (int j = 0; j < A[0].length; j++) {
                // 注意类型转换
                long temp = 0;
                for (int k = 0; k < A.length; k++) {
                    temp += (long) A[i][k] * B[k][j];
                }
                res[i][j] = (int) (temp % 1000000007);
                // 知道是二阶的简易写法
                // res[i][j] = (int) (((long) A[i][0] * B[0][j] + (long) A[i][1] * B[1][j]) % 1000000007);
            }
        }
        return res;
    }
}
```
