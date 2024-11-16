# Treap树实现有序表

## [P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)

> - **Question**
>   - 您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：
>     1. 插入一个数 `x` 。
>     2. 删除一个数 `x` （若有多个相同的数，应只删除一个）。
>     3. 定义排名为比当前数小的数的个数 `+ 1` 。查询 `x` 的排名。
>     4. 查询数据结构中排名为 `x` 的数。
>     5. 求 `x` 的前驱（前驱定义为小于 `x` ，且最大的数）。
>     6. 求 `x` 的后继（后继定义为大于 `x` ，且最小的数）。
>   - 对于操作 `3, 5, 6` ，不保证当前数据结构中存在数 `x` 。
>   - **输入描述**
>     - 第一行为 `n` ，表示操作的个数,下面 `n` 行每行有两个数 `opt` 和 `x` ， `opt` 表示操作的序号（ `1 <= opt <= 6` ）。
>   - **输出描述**
>     - 对于操作 `3, 4, 5, 6` 每行输出一个数，表示对应答案。
>   - **Tips**
>     - `1 <=n <= 10^5`
>     - `|x| <= 10^7`

## Java

> - **Question**

```java
// Treap树就是笛卡尔树，key按搜索二叉树组织，priority代表节点的优先级，是随机的，按堆组织

// 插入(key, priority)这个节点时，先根据key在搜索二叉树上移动，确定插入的位置
// 然后根据priority的大小，决定节点是否向上移动，向上移动通过左旋、右旋来实现

// 删除(key, priority)这个节点时，先在搜索二叉树上，找到key的节点所在的位置
// 1，无左无右，直接删掉。2，有左无右，左孩子接替其位置。3，无左有右，右孩子接替其位置
// 4，如果节点有左有右，那么优先级最大的孩子接替其位置，可以利用左旋、右旋实现
// 此时节点被旋转到了或往左、或往右的子树里，继续在子树上删掉该节点

// 随机优先级的方式，保证了Treap树的平衡性，增删改查时间复杂度O(log n)，非常类似跳表

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    // 整棵树的头节点编号
    public static int head = 0;

    // 空间使用计数
    public static int cnt = 0;

    // 节点的key值
    public static int[] key = new int[MAXN];

    // 节点key的计数
    public static int[] count = new int[MAXN];

    // 左孩子
    public static int[] left = new int[MAXN];

    // 右孩子
    public static int[] right = new int[MAXN];

    // 数字总数
    public static int[] size = new int[MAXN];

    // 节点优先级
    public static double[] priority = new double[MAXN];

    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + count[i];
    }

    public static int leftRotate(int i) {
        int r = right[i];
        right[i] = left[r];
        left[r] = i;
        up(i);
        up(r);
        return r;
    }

    public static int rightRotate(int i) {
        int l = left[i];
        left[i] = right[l];
        right[l] = i;
        up(i);
        up(l);
        return l;
    }

    public static int add(int i, int num) {
        if (i == 0) {
        }
        return i;
    }

    public static void add(int num) {
        head = add(head, num);
    }

    public static int small(int i, int num) {
        if (i == 0) {
            return 0;
        }
        if (key[i] >= num) {
            return small(left[i], num);
        } else {
            return size[left[i]] + count[i] + small(right[i], num);
        }
    }

    public static int rank(int num) {
        return small(head, num) + 1;
    }

    public static int index(int i, int x) {
        if (size[left[i]] >= x) {
            return index(left[i], x);
        } else if (size[left[i]] + count[i] < x) {
            return index(right[i], x - size[left[i]] - count[i]);
        }
        return key[i];
    }

    public static int index(int x) {
        return index(head, x);
    }

    public static int pre(int i, int num) {
        if (i == 0) {
            return Integer.MIN_VALUE;
        }
        if (key[i] >= num) {
            return pre(left[i], num);
        } else {
            return Math.max(key[i], pre(right[i], num));
        }
    }

    public static int pre(int num) {
        return pre(head, num);
    }

    public static int post(int i, int num) {
        if (i == 0) {
            return Integer.MAX_VALUE;
        }
        if (key[i] <= num) {
            return post(right[i], num);
        } else {
            return Math.min(key[i], post(left[i], num));
        }
    }

    public static int post(int num) {
        return post(head, num);
    }

    public static int remove(int i, int num) {
        if (key[i] < num) {
            right[i] = remove(right[i], num);
        } else if (key[i] > num) {
            left[i] = remove(left[i], num);
        } else {
            if (count[i] > 1) {
                count[i]--;
            } else {
                if (left[i] == 0 && right[i] == 0) {
                    return 0;
                } else if (left[i] != 0 && right[i] == 0) {
                    i = left[i];
                } else if (left[i] == 0 && right[i] != 0) {
                    i = right[i];
                } else {
                    if (priority[left[i]] >= priority[right[i]]) {
                        i = rightRotate(i);
                        right[i] = remove(right[i], num);
                    } else {
                        i = leftRotate(i);
                        left[i] = remove(left[i], num);
                    }
                }
            }
        }
        up(i);
        return i;
    }

    public static void remove(int num) {
        if (rank(num) != rank(num + 1)) {
            head = remove(head, num);
        }
    }

    public static void clear() {
        Arrays.fill(key, 1, cnt + 1, 0);
        Arrays.fill(count, 1, cnt + 1, 0);
        Arrays.fill(left, 1, cnt + 1, 0);
        Arrays.fill(right, 1, cnt + 1, 0);
        Arrays.fill(size, 1, cnt + 1, 0);
        Arrays.fill(priority, 1, cnt + 1, 0);
        cnt = 0;
        head = 0;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1, op, x; i <= n; i++) {
            in.nextToken();
            op = (int) in.nval;
            in.nextToken();
            x = (int) in.nval;
            if (op == 1) {
                add(x);
            } else if (op == 2) {
                remove(x);
            } else if (op == 3) {
                out.println(rank(x));
            } else if (op == 4) {
                out.println(index(x));
            } else if (op == 5) {
                out.println(pre(x));
            } else {
                out.println(post(x));
            }
        }
        clear();
        out.flush();
        out.close();
        br.close();
    }

}
```
