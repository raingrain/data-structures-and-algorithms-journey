# FHQ-Treap实现有序表

## [P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)

> - **Question**
>   - 您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：
>     1. 插入一个数 `x` 。
>     2. 删除一个数 `x` （若有多个相同的数，应只删除一个）。
>     3. 定义排名为比当前数小的数的个数 `+ 1` 。查询 `x` 的排名。
>     4. 查询数据结构中排名为 `x` 的数。
>     5. 求 `x` 的前驱（前驱定义为小于 `x` ，且最大的数）。
>     6. 求 `x` 的后继（后继定义为大于 `x`，且最小的数）。
>     - 对于操作 `3, 5, 6` ，不保证当前数据结构中存在数 `x` 。
>   - **输入描述**
>     - 第一行为 `n` ，表示操作的个数,下面 `n` 行每行有两个数 `opt` 和 `x` ， `opt` 表示操作的序号（ `1 <= opt <= 6）
>   - **输出描述**
>     - 对于操作 `3, 4, 5, 6` 每行输出一个数，表示对应答案。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `|x| <= 10^7`

## Java

> - **使用词频压缩**

```java
import java.io.*;

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

    public static void split(int l, int r, int i, int num) {
        if (i == 0) {
            right[l] = left[r] = 0;
        } else {
            if (key[i] <= num) {
                right[l] = i;
                split(i, r, right[i], num);
            } else {
                left[r] = i;
                split(l, i, left[i], num);
            }
            up(i);
        }
    }

    public static int merge(int l, int r) {
        if (l == 0 || r == 0) {
            return l + r;
        }
        if (priority[l] >= priority[r]) {
            right[l] = merge(right[l], r);
            up(l);
            return l;
        } else {
            left[r] = merge(l, left[r]);
            up(r);
            return r;
        }
    }

    public static int find(int i, int num) {
        if (i == 0) {
            return 0;
        }
        if (key[i] == num) {
            return i;
        } else if (key[i] > num) {
            return find(left[i], num);
        } else {
            return find(right[i], num);
        }
    }

    public static void changeCount(int i, int num, int change) {
        if (key[i] == num) {
            count[i] += change;
        } else if (key[i] > num) {
            changeCount(left[i], num, change);
        } else {
            changeCount(right[i], num, change);
        }
        up(i);
    }

    public static void add(int num) {
        if (find(head, num) != 0) {
            changeCount(head, num, 1);
        } else {
            split(0, 0, head, num);
            key[++cnt] = num;
            count[cnt] = size[cnt] = 1;
            priority[cnt] = Math.random();
            head = merge(merge(right[0], cnt), left[0]);
        }
    }

    public static void remove(int num) {
        int i = find(head, num);
        if (i != 0) {
            if (count[i] > 1) {
                changeCount(head, num, -1);
            } else {
                split(0, 0, head, num);
                int lm = right[0];
                int r = left[0];
                split(0, 0, lm, num - 1);
                int l = right[0];
                head = merge(l, r);
            }
        }
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
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **不使用词频压缩**
>   - 常规实现。

```java
// 注意，FHQ-Treap可持久化和线段树可持久化，设计非常相似
// FHQ-Treap是最常用的有序表结构、可持久化有序表结构，原理简单、实现简单、扩展性好、常数时间好

// 左树上的key <= 头节点的key <= 右树上的key，其实这种设定是FHQ-Treap最常规的设定

import java.io.*;

public class Main {

    public static int MAXN = 100001;

    // 整棵树的头节点编号
    public static int head = 0;

    // 空间使用计数
    public static int cnt = 0;

    // 节点的key值
    public static int[] key = new int[MAXN];

    // 左孩子
    public static int[] left = new int[MAXN];

    // 右孩子
    public static int[] right = new int[MAXN];

    // 节点总数
    public static int[] size = new int[MAXN];

    // 节点优先级
    public static double[] priority = new double[MAXN];

    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + 1;
    }

    // 可以根据key分裂树，<=key的节点变成一棵搜索二叉树，>key的节点变成另一棵搜索二叉树
    // 也可根据排名分裂树，<=排名的节点变成一棵搜索二叉树，>排名的节点变成另一棵搜索二叉树
    // 最终分别得到两棵树的头节点编号，<=部分的头记录在right[0]，>部分的头记录在left[0]
    public static void split(int l, int r, int i, int num) {
        if (i == 0) {
            right[l] = left[r] = 0;
        } else {
            if (key[i] <= num) {
                right[l] = i;
                split(i, r, right[i], num);
            } else {
                left[r] = i;
                split(l, i, left[i], num);
            }
            up(i);
        }
    }

    // 给定两棵搜索二叉树的头l和r，要求，l整棵树的key值或排名值 总体小于 r整棵树的key值或排名值
    // 每一步谁优先级大谁做头，合并成一棵树，最终返回整棵树的头节点编号
    public static int merge(int l, int r) {
        if (l == 0 || r == 0) {
            return l + r;
        }
        if (priority[l] >= priority[r]) {
            right[l] = merge(right[l], r);
            up(l);
            return l;
        } else {
            left[r] = merge(l, left[r]);
            up(r);
            return r;
        }
    }

    public static void add(int num) {
        split(0, 0, head, num);
        key[++cnt] = num;
        size[cnt] = 1;
        priority[cnt] = Math.random();
        head = merge(merge(right[0], cnt), left[0]);
    }

    public static void remove(int num) {
        split(0, 0, head, num);
        int lm = right[0];
        int r = left[0];
        split(0, 0, lm, num - 1);
        int l = right[0];
        int m = left[0];
        head = merge(merge(l, merge(left[m], right[m])), r);
    }

    public static int rank(int num) {
        split(0, 0, head, num - 1);
        int ans = size[right[0]] + 1;
        head = merge(right[0], left[0]);
        return ans;
    }

    public static int index(int i, int x) {
        if (size[left[i]] >= x) {
            return index(left[i], x);
        } else if (size[left[i]] + 1 < x) {
            return index(right[i], x - size[left[i]] - 1);
        } else {
            return key[i];
        }
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
        out.flush();
        out.close();
        br.close();
    }

}
```
