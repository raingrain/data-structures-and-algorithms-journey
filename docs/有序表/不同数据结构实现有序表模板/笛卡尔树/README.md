# 笛卡尔树

## [P5854 【模板】笛卡尔树](https://www.luogu.com.cn/problem/P5854)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，下标从 `1` 开始，构建一棵二叉树，下标按照搜索二叉树组织，值按照小根堆组织，建树的过程要求时间复杂度 `O(n)` ，建树之后，为了验证，打印 `i * (left[i] + 1)` 所有信息异或起来的值，打印 `i* (right[i] + 1)` 所有信息异或起来的值。
>   - **Tips**
>     - `1 <= n <= 10^7`

## Java

> - **Question**

```java
// 一般默认key没有相同值，key按照搜索二叉树组织，value按照小根堆或者大根堆组织
// 不是狭义的小根堆或者大根堆
// 整棵子树的最小值或最大值，一定是子树的头，但不要求子树一定为完全二叉树，这种广义的堆

// 笛卡尔树建树过程，时间复杂度O(n)
// 1，当前插入节点假设为x，依据x的value值，在单调栈中依次弹出节点
// 2，最晚弹出的节点y及其整棵子树，成为x的左树
// 3，假设x在单调栈中压着z节点，那么x就成为z的右孩子
// 4，节点x根据value值加入单调栈

import java.io.*;

public class Main {

    public static int MAXN = 10000001;

    public static int[] arr = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] stack = new int[MAXN];

    public static int n;

    public static void build() {
        for (int i = 1, top = 0, pos = 0; i <= n; i++) {
            pos = top;
            while (pos > 0 && arr[stack[pos]] > arr[i]) {
                pos--;
            }
            if (pos > 0) {
                right[stack[pos]] = i;
            }
            if (pos < top) {
                left[i] = stack[pos + 1];
            }
            stack[++pos] = i;
            top = pos;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        n = (int) in.nval;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            arr[i] = (int) in.nval;
        }
        build();
        long ans1 = 0, ans2 = 0;
        for (int i = 1; i <= n; i++) {
            ans1 ^= (long) i * (left[i] + 1);
            ans2 ^= (long) i * (right[i] + 1);
        }
        out.println(ans1 + " " + ans2);
        out.flush();
        out.close();
        br.close();
    }

}
```
