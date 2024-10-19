# 高斯消元解决加法方程组

## [P3389 【模板】高斯消元法](https://www.luogu.com.cn/problem/P3389)

> - ***Question 1***
>   - 一共有 `n` 个变量，给定 `n` 个加法方程，构成一个加法方程组。
>   - 如果方程组存在矛盾或者无法确定唯一解，打印 `No Solution` 。
>   - 如果方程组存在唯一解，打印每个变量的值，保留小数点后两位。
>   - ***tips:***
>     - `1 <= n <= 100`

## [P2455 [SDOI2006] 线性方程组](https://www.luogu.com.cn/problem/P2455)

> - ***Question 2***
>   - 一共有 `n` 个变量，给定 `n` 个加法方程，构成一个加法方程组。
>   - 如果方程组存在矛盾，打印 `-1` 。
>   - 如果方程组无法确定唯一解，打印 `0` 。
>   - 如果方程组存在唯一解，打印每个变量的值，保留小数点后两位。
>   - ***tips:***
>     - `1 <= n <= 50`

---

## *Java*

> - ***高斯消元模板***
>   - 时间复杂度 `O(n^3)` ， `n` 为扩充后的方程数。

```java
public class Main {

    public static int MAXN = 101;

    public static double[][] mat = new double[MAXN][MAXN];

    // 0.0000001 == 1e-7
    // 因为double类型有精度问题，所以认为
    // 如果一个数字绝对值 < sml，则认为该数字是0
    // 如果一个数字绝对值>= sml，则认为该数字不是0
    public static double sml = 1e-7;

    // 高斯消元解决加法方程组模版
    // 需要保证变量有n个，表达式也有n个
    public static void gauss(int n) {
        // i是列
        for (int i = 1; i <= n; i++) {
            // 如果想严格区分矛盾、多解、唯一解，一定要这么写
            int max = i;
            // j是行
            for (int j = 1; j <= n; j++) {
                // 不是主元的不参加
                if (j < i && Math.abs(mat[j][j]) >= sml) {
                    continue;
                }
                // 谁最大换到第i行
                if (Math.abs(mat[j][i]) > Math.abs(mat[max][i])) {
                    max = j;
                }
            }
            // 换到那行
            swap(i, max);
            if (Math.abs(mat[i][i]) >= sml) {
                double tmp = mat[i][i];
                // 本行先干成1
                for (int j = i; j <= n + 1; j++) {
                    mat[i][j] /= tmp;
                }
                // 减成0
                for (int j = 1; j <= n; j++) {
                    if (i != j) {
                        double rate = mat[j][i] / mat[i][i];
                        for (int k = i; k <= n + 1; k++) {
                            mat[j][k] -= mat[i][k] * rate;
                        }
                    }
                }
            }
        }
    }

    public static void swap(int a, int b) {
        double[] tmp = mat[a];
        mat[a] = mat[b];
        mat[b] = tmp;
    }

    public static void print(int n) {
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n + 1; j++) {
                System.out.printf("%.2f  ", mat[i][j]);
            }
            System.out.println();
        }
        System.out.println("========================");
    }

    public static void main(String[] args) {
        // 唯一解
        // 1*x1 + 2*x2 - 1*x3 = 9
        // 2*x1 - 1*x2 + 2*x3 = 7
        // 1*x1 - 2*x2 + 2*x3 = 0
        System.out.println("唯一解");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 9;
        mat[2][1] = 2;
        mat[2][2] = -1;
        mat[2][3] = 2;
        mat[2][4] = 7;
        mat[3][1] = 1;
        mat[3][2] = -2;
        mat[3][3] = 2;
        mat[3][4] = 0;
        gauss(3);
        print(3);

        // 矛盾
        // 1*x1 + 1*x2 = 3
        // 2*x1 + 2*x2 = 7
        System.out.println("矛盾");
        mat[1][1] = 1;
        mat[1][2] = 1;
        mat[1][3] = 3;
        mat[2][1] = 2;
        mat[2][2] = 2;
        mat[2][3] = 7;
        gauss(2);
        print(2);

        // 多解
        // 1*x1 + 1*x2 = 3
        // 2*x1 + 2*x2 = 6
        System.out.println("多解");
        mat[1][1] = 1;
        mat[1][2] = 1;
        mat[1][3] = 3;
        mat[2][1] = 2;
        mat[2][2] = 2;
        mat[2][3] = 6;
        gauss(2);
        print(2);

        // 表达式冗余，唯一解
        // 1*x1 + 2*x2 - 1*x3 + 0*x4 = 9
        // 2*x1 + 4*x2 - 2*x3 + 0*x4 = 18
        // 2*x1 - 1*x2 + 2*x3 + 0*x4 = 7
        // 1*x1 - 2*x2 + 2*x3 + 0*x4 = 0
        System.out.println("表达式冗余，唯一解");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 0;
        mat[1][5] = 9;
        mat[2][1] = 2;
        mat[2][2] = 4;
        mat[2][3] = -2;
        mat[2][4] = 0;
        mat[2][5] = 18;
        mat[3][1] = 2;
        mat[3][2] = -1;
        mat[3][3] = 2;
        mat[3][4] = 0;
        mat[3][5] = 7;
        mat[4][1] = 1;
        mat[4][2] = -2;
        mat[4][3] = 2;
        mat[4][4] = 0;
        mat[4][5] = 0;
        gauss(4);
        print(4);

        // 表达式冗余，矛盾
        // 1*x1 + 2*x2 - 1*x3 = 9
        // 2*x1 + 4*x2 - 2*x3 = 18
        // 2*x1 - 1*x2 + 2*x3 = 7
        // 4*x1 - 2*x2 + 4*x3 = 10
        System.out.println("表达式冗余，矛盾");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 0;
        mat[1][5] = 9;
        mat[2][1] = 2;
        mat[2][2] = 4;
        mat[2][3] = -2;
        mat[2][4] = 0;
        mat[2][5] = 18;
        mat[3][1] = 2;
        mat[3][2] = -1;
        mat[3][3] = 2;
        mat[3][4] = 0;
        mat[3][5] = 7;
        mat[4][1] = 4;
        mat[4][2] = -2;
        mat[4][3] = 4;
        mat[4][4] = 0;
        mat[4][5] = 10;
        gauss(4);
        print(4);

        // 表达式冗余，多解
        // 1*x1 + 2*x2 - 1*x3 = 9
        // 2*x1 + 4*x2 - 2*x3 = 18
        // 2*x1 - 1*x2 + 2*x3 = 7
        // 4*x1 - 2*x2 + 4*x3 = 14
        System.out.println("表达式冗余，多解");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 0;
        mat[1][5] = 9;
        mat[2][1] = 2;
        mat[2][2] = 4;
        mat[2][3] = -2;
        mat[2][4] = 0;
        mat[2][5] = 18;
        mat[3][1] = 2;
        mat[3][2] = -1;
        mat[3][3] = 2;
        mat[3][4] = 0;
        mat[3][5] = 7;
        mat[4][1] = 4;
        mat[4][2] = -2;
        mat[4][3] = 4;
        mat[4][4] = 0;
        mat[4][5] = 14;
        gauss(4);
        print(4);

        // 表达式不足，矛盾
        // 1*x1 + 2*x2 - 1*x3 = 5
        // 2*x1 + 4*x2 - 2*x3 = 7
        System.out.println("表达式不足，矛盾");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 5;
        mat[2][1] = 2;
        mat[2][2] = 4;
        mat[2][3] = -2;
        mat[2][4] = 7;
        mat[3][1] = 0;
        mat[3][2] = 0;
        mat[3][3] = 0;
        mat[3][4] = 0;
        gauss(3);
        print(3);

        // 表达式不足，多解
        // 1*x1 + 2*x2 - 1*x3 = 5
        // 2*x1 + 2*x2 - 1*x3 = 8
        System.out.println("表达式不足，多解");
        mat[1][1] = 1;
        mat[1][2] = 2;
        mat[1][3] = -1;
        mat[1][4] = 5;
        mat[2][1] = 2;
        mat[2][2] = 2;
        mat[2][3] = -1;
        mat[2][4] = 8;
        mat[3][1] = 0;
        mat[3][2] = 0;
        mat[3][3] = 0;
        mat[3][4] = 0;
        gauss(3);
        print(3);

        // 正确区分矛盾、多解、唯一解
        // 0*x1 + 2*x2 = 3
        // 0*x1 + 0*x2 = 0
        System.out.println("正确区分矛盾、多解、唯一解");
        mat[1][1] = 0;
        mat[1][2] = 2;
        mat[1][3] = 3;
        mat[2][1] = 0;
        mat[2][2] = 0;
        mat[2][3] = 0;
        gauss(2);
        print(2);

        // 有些主元可以确定值
        // a  + b + c = 5
        // 2a + b + c = 7
        System.out.println("有些主元可以确定值");
        mat[1][1] = 1;
        mat[1][2] = 1;
        mat[1][3] = 1;
        mat[1][4] = 5;
        mat[2][1] = 2;
        mat[2][2] = 1;
        mat[2][3] = 1;
        mat[2][4] = 7;
        mat[3][1] = 0;
        mat[3][2] = 0;
        mat[3][3] = 0;
        mat[3][4] = 0;
        gauss(3);
        print(3);

        // 有些主元还受到自由元的影响
        // a + b = 5
        System.out.println("有些主元还受到自由元的影响");
        mat[1][1] = 1;
        mat[1][2] = 1;
        mat[1][3] = 5;
        mat[2][1] = 0;
        mat[2][2] = 0;
        mat[2][3] = 0;
        gauss(2);
        print(2);
    }

}
```

> - ***Question 1: 高斯消元解决加法方程组模版（区分是否有唯一解）***

```java
import java.io.*;

public class Main {

    public static int MAXN = 102;

    public static double[][] mat = new double[MAXN][MAXN];

    public static int n;

    // 0.0000001 == 1e-7
    // 因为double类型有精度问题，所以认为
    // 如果一个数字绝对值 <  sml，则认为该数字是0
    // 如果一个数字绝对值 >= sml，则认为该数字不是0
    public static double sml = 1e-7;

    // 需要保证变量有n个，表达式也有n个
    public static int gauss(int n) {
        for (int i = 1; i <= n; i++) {
            // 本题一旦没有唯一解，直接打印"No Solution"，于是可以这么写
            int max = i;
            for (int j = i + 1; j <= n; j++) {
                if (Math.abs(mat[j][i]) > Math.abs(mat[max][i])) {
                    max = j;
                }
            }
            swap(i, max);
            if (Math.abs(mat[i][i]) < sml) {
                return 0;
            }
            double tmp = mat[i][i];
            for (int j = i; j <= n + 1; j++) {
                mat[i][j] /= tmp;
            }
            for (int j = 1; j <= n; j++) {
                if (i != j) {
                    double rate = mat[j][i] / mat[i][i];
                    for (int k = i; k <= n + 1; k++) {
                        mat[j][k] -= mat[i][k] * rate;
                    }
                }
            }
        }
        return 1;
    }

    public static void swap(int a, int b) {
        double[] tmp = mat[a];
        mat[a] = mat[b];
        mat[b] = tmp;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n + 1; j++) {
                in.nextToken();
                mat[i][j] = (double) in.nval;
            }
        }
        int sign = gauss(n);
        if (sign == 0) {
            out.println("No Solution");
        } else {
            for (int i = 1; i <= n; i++) {
                out.printf("%.2f\n", mat[i][n + 1]);
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

> - ***Question 2: 高斯消元解决加法方程组模版（区分矛盾、多解、唯一解）***

```java
import java.io.*;

public class Main {

    public static int MAXN = 52;

    public static double[][] mat = new double[MAXN][MAXN];

    public static int n;

    // 0.0000001 == 1e-7
    // 因为double类型有精度问题，所以认为
    // 如果一个数字绝对值 <  sml，则认为该数字是0
    // 如果一个数字绝对值 >= sml，则认为该数字不是0
    public static double sml = 1e-7;

    // 高斯消元解决加法方程组模版
    // 需要保证变量有n个，表达式也有n个
    public static void gauss(int n) {
        for (int i = 1; i <= n; i++) {
            // 本题需要严格区分矛盾、多解、唯一解，所以必须这么写
            int max = i;
            for (int j = 1; j <= n; j++) {
                if (j < i && Math.abs(mat[j][j]) >= sml) {
                    continue;
                }
                if (Math.abs(mat[j][i]) > Math.abs(mat[max][i])) {
                    max = j;
                }
            }
            swap(i, max);
            if (Math.abs(mat[i][i]) >= sml) {
                double tmp = mat[i][i];
                for (int j = i; j <= n + 1; j++) {
                    mat[i][j] /= tmp;
                }
                for (int j = 1; j <= n; j++) {
                    if (i != j) {
                        double rate = mat[j][i] / mat[i][i];
                        for (int k = i; k <= n + 1; k++) {
                            mat[j][k] -= mat[i][k] * rate;
                        }
                    }
                }
            }
        }
    }

    public static void swap(int a, int b) {
        double[] tmp = mat[a];
        mat[a] = mat[b];
        mat[b] = tmp;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n + 1; j++) {
                in.nextToken();
                mat[i][j] = (double) in.nval;
            }
        }
        gauss(n);
        int sign = 1;
        for (int i = 1; i <= n; i++) {
            if (Math.abs(mat[i][i]) < sml && Math.abs(mat[i][n + 1]) >= sml) {
                sign = -1;
                break;
            }
            if (Math.abs(mat[i][i]) < sml) {
                sign = 0;
            }
        }
        if (sign == 1) {
            for (int i = 1; i <= n; i++) {
                out.printf("x" + i + "=" + "%.2f\n", mat[i][n + 1]);
            }
        } else {
            out.println(sign);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```

---

> ***last change: 2024/7/19***

---
