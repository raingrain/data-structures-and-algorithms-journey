# Splay树实现有序表

## Splay树的势能分析

![image](./images/Splay树的势能分析.png)

## [P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)

> - **Question 1**
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

## [P6136 【模板】普通平衡树（数据加强版）](https://www.luogu.com.cn/problem/P6136)

> - **Question 2**
>   - 本题是 `Question 1` 数据加强版，扩大数据范围并增加了强制在线。题目的输入、输出和原题略有不同，但需要支持的操作相同。

## Java

> - **Question 1: 不用词频压缩实现Splay树**

```java
// Splay树的注意点
// 1，初始化时，可以使分的式递归建出完美的平衡树，其实没有必要，势能分析的证明过程说明，初始不管平衡还是不平衡，都不会影响单次均摊的时间复杂度，哪怕出现极度不平衡的情况，随着操作的进，链会迅速变短，树会快速变成近乎平衡的状况
// 2，Splay树单次操作的时间复杂度O(log n)，但是常数项时间较
// 3，不管是插、删除、查询，要保证都有提根操作，让链得到缩减机会
// 4，Splay树的实现，慎递归，虽然链会得到缩减，但仍可能出现链，让递归爆栈
// 5，多数时候，Splay树可以被FHQ-Treap替代
// 6，可持久化Splay树不需要掌握，因为空间耗损较，掌握可持久化FHQ-Treap即可
// 7，Splay树具有独特的提根操作，可以帮助实现Link-Cut-Tree

// Splay树维护的搜索叉树为，左 < 头 <= 右
// 加过程、删除过程都有提根操作，同时要保证每种查询功能都有提根操作！这很重要！

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;

    public static int head = 0;

    public static int cnt = 0;

    public static int[] key = new int[MAXN];

    public static int[] father = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] size = new int[MAXN];

    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + 1;
    }

    public static int lr(int i) {
        return right[father[i]] == i ? 1 : 0;
    }

    public static void rotate(int i) {
        int f = father[i], g = father[f], soni = lr(i), sonf = lr(f);
        if (soni == 1) {
            right[f] = left[i];
            if (right[f] != 0) {
                father[right[f]] = f;
            }
            left[i] = f;
        } else {
            left[f] = right[i];
            if (left[f] != 0) {
                father[left[f]] = f;
            }
            right[i] = f;
        }
        if (g != 0) {
            if (sonf == 1) {
                right[g] = i;
            } else {
                left[g] = i;
            }
        }
        father[f] = i;
        father[i] = g;
        up(f);
        up(i);
    }

    public static void splay(int i, int goal) {
        int f = father[i], g = father[f];
        while (f != goal) {
            if (g != goal) {
                if (lr(i) == lr(f)) {
                    rotate(f);
                } else {
                    rotate(i);
                }
            }
            rotate(i);
            f = father[i];
            g = father[f];
        }
        if (goal == 0) {
            head = i;
        }
    }

    // 整棵树上找到中序排名为rank的节点，返回节点编号
    // 这个方法不是题目要求的查询操作，作为内部方法使用
    // 为什么该方法不进行提根操作？
    // 因为remove方法使用该方法时，要求find不能提根！
    public static int find(int rank) {
        int i = head;
        while (i != 0) {
            if (size[left[i]] + 1 == rank) {
                return i;
            } else if (size[left[i]] >= rank) {
                i = left[i];
            } else {
                rank -= size[left[i]] + 1;
                i = right[i];
            }
        }
        return 0;
    }

    public static void add(int num) {
        key[++cnt] = num;
        size[cnt] = 1;
        if (head == 0) {
            head = cnt;
        } else {
            int f = 0, i = head, son = 0;
            while (i != 0) {
                f = i;
                if (key[i] <= num) {
                    son = 1;
                    i = right[i];
                } else {
                    son = 0;
                    i = left[i];
                }
            }
            if (son == 1) {
                right[f] = cnt;
            } else {
                left[f] = cnt;
            }
            father[cnt] = f;
            splay(cnt, 0);
        }
    }

    public static int rank(int num) {
        int i = head, last = head;
        int ans = 0;
        while (i != 0) {
            last = i;
            if (key[i] >= num) {
                i = left[i];
            } else {
                ans += size[left[i]] + 1;
                i = right[i];
            }
        }
        splay(last, 0);
        return ans + 1;
    }

    public static int index(int x) {
        int i = find(x);
        splay(i, 0);
        return key[i];
    }

    public static int pre(int num) {
        int i = head, last = head;
        int ans = Integer.MIN_VALUE;
        while (i != 0) {
            last = i;
            if (key[i] >= num) {
                i = left[i];
            } else {
                ans = Math.max(ans, key[i]);
                i = right[i];
            }
        }
        splay(last, 0);
        return ans;
    }

    public static int post(int num) {
        int i = head, last = head;
        int ans = Integer.MAX_VALUE;
        while (i != 0) {
            last = i;
            if (key[i] <= num) {
                i = right[i];
            } else {
                ans = Math.min(ans, key[i]);
                i = left[i];
            }
        }
        splay(last, 0);
        return ans;
    }

    public static void remove(int num) {
        int kth = rank(num);
        if (kth != rank(num + 1)) {
            int i = find(kth);
            splay(i, 0);
            if (left[i] == 0) {
                head = right[i];
            } else if (right[i] == 0) {
                head = left[i];
            } else {
                int j = find(kth + 1);
                splay(j, i);
                left[j] = left[i];
                father[left[j]] = j;
                up(j);
                head = j;
            }
            father[head] = 0;
        }
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

> - **Question 2: Splay树实现普通有序表（不用词频压缩）**

```java
import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 2000001;

    public static int head = 0;

    public static int cnt = 0;

    public static int[] key = new int[MAXN];

    public static int[] father = new int[MAXN];

    public static int[] left = new int[MAXN];

    public static int[] right = new int[MAXN];

    public static int[] size = new int[MAXN];

    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + 1;
    }

    public static int lr(int i) {
        return right[father[i]] == i ? 1 : 0;
    }

    public static void rotate(int i) {
        int f = father[i], g = father[f], soni = lr(i), sonf = lr(f);
        if (soni == 1) {
            right[f] = left[i];
            if (right[f] != 0) {
                father[right[f]] = f;
            }
            left[i] = f;
        } else {
            left[f] = right[i];
            if (left[f] != 0) {
                father[left[f]] = f;
            }
            right[i] = f;
        }
        if (g != 0) {
            if (sonf == 1) {
                right[g] = i;
            } else {
                left[g] = i;
            }
        }
        father[f] = i;
        father[i] = g;
        up(f);
        up(i);
    }

    public static void splay(int i, int goal) {
        int f = father[i], g = father[f];
        while (f != goal) {
            if (g != goal) {
                if (lr(i) == lr(f)) {
                    rotate(f);
                } else {
                    rotate(i);
                }
            }
            rotate(i);
            f = father[i];
            g = father[f];
        }
        if (goal == 0) {
            head = i;
        }
    }

    // 整棵树上找到中序排名为rank的节点，返回节点编号
    // 这个方法不是题目要求的查询操作，作为内部方法使用
    // 为什么该方法不进行提根操作？
    // 因为remove方法使用该方法时，要求find不能提根！
    public static int find(int rank) {
        int i = head;
        while (i != 0) {
            if (size[left[i]] + 1 == rank) {
                return i;
            } else if (size[left[i]] >= rank) {
                i = left[i];
            } else {
                rank -= size[left[i]] + 1;
                i = right[i];
            }
        }
        return 0;
    }

    public static void add(int num) {
        key[++cnt] = num;
        size[cnt] = 1;
        if (head == 0) {
            head = cnt;
        } else {
            int f = 0, i = head, son = 0;
            while (i != 0) {
                f = i;
                if (key[i] <= num) {
                    son = 1;
                    i = right[i];
                } else {
                    son = 0;
                    i = left[i];
                }
            }
            if (son == 1) {
                right[f] = cnt;
            } else {
                left[f] = cnt;
            }
            father[cnt] = f;
            splay(cnt, 0);
        }
    }

    public static int rank(int num) {
        int i = head, last = head;
        int ans = 0;
        while (i != 0) {
            last = i;
            if (key[i] >= num) {
                i = left[i];
            } else {
                ans += size[left[i]] + 1;
                i = right[i];
            }
        }
        splay(last, 0);
        return ans + 1;
    }

    public static int index(int x) {
        int i = find(x);
        splay(i, 0);
        return key[i];
    }

    public static int pre(int num) {
        int i = head, last = head;
        int ans = Integer.MIN_VALUE;
        while (i != 0) {
            last = i;
            if (key[i] >= num) {
                i = left[i];
            } else {
                ans = Math.max(ans, key[i]);
                i = right[i];
            }
        }
        splay(last, 0);
        return ans;
    }

    public static int post(int num) {
        int i = head, last = head;
        int ans = Integer.MAX_VALUE;
        while (i != 0) {
            last = i;
            if (key[i] <= num) {
                i = right[i];
            } else {
                ans = Math.min(ans, key[i]);
                i = left[i];
            }
        }
        splay(last, 0);
        return ans;
    }

    public static void remove(int num) {
        int kth = rank(num);
        if (kth != rank(num + 1)) {
            int i = find(kth);
            splay(i, 0);
            if (left[i] == 0) {
                head = right[i];
            } else if (right[i] == 0) {
                head = left[i];
            } else {
                int j = find(kth + 1);
                splay(j, i);
                left[j] = left[i];
                father[left[j]] = j;
                up(j);
                head = j;
            }
            father[head] = 0;
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int m = (int) in.nval;
        for (int i = 1, num; i <= n; i++) {
            in.nextToken();
            num = (int) in.nval;
            add(num);
        }
        int lastAns = 0;
        int ans = 0;
        for (int i = 1, op, x; i <= m; i++) {
            in.nextToken();
            op = (int) in.nval;
            in.nextToken();
            x = (int) in.nval ^ lastAns;
            if (op == 1) {
                add(x);
            } else if (op == 2) {
                remove(x);
            } else if (op == 3) {
                lastAns = rank(x);
                ans ^= lastAns;
            } else if (op == 4) {
                lastAns = index(x);
                ans ^= lastAns;
            } else if (op == 5) {
                lastAns = pre(x);
                ans ^= lastAns;
            } else {
                lastAns = post(x);
                ans ^= lastAns;
            }
        }
        out.println(ans);
        out.flush();
        out.close();
        br.close();
    }

}
```

> - **提根操作的代码 + 长链高度变化的实验**
>   ![image](./images/Splay树的提根操作.png)

```java
// 每一种长链都让最下方节点提根上去
// 然后看看长链的高度变化
public class Main {

    public static int MAXN = 100001;

    // 整棵树的头节点编号
    public static int head = 0;

    // 分配了多少节点编号
    public static int cnt = 0;

    // key[i]    : 编号为i的节点的key
    public static int[] key = new int[MAXN];

    // father[i] : 编号为i的节点的父节点编号
    public static int[] father = new int[MAXN];

    // left[i]   : 编号为i的节点的左孩子编号
    public static int[] left = new int[MAXN];

    // right[i]  : 编号为i的节点的右孩子编号
    public static int[] right = new int[MAXN];

    // size[i]   : 以编号为i的节点做头的子树，一共有多少个节点
    //             这个数组和具体要实现的功能有关，属于常用信息
    public static int[] size = new int[MAXN];

    public static void up(int i) {
        size[i] = size[left[i]] + size[right[i]] + 1;
    }

    // 判断编号为i的节点，是其父亲的什么儿子
    // 0代表左儿子，1代表右儿子
    public static int lr(int i) {
        return right[father[i]] == i ? 1 : 0;
    }

    // 编号为i的节点上升一步，在结构上做调整
    public static void rotate(int i) {
        int f = father[i], g = father[f], soni = lr(i), sonf = lr(f);
        if (soni == 1) {
            right[f] = left[i];
            if (right[f] != 0) {
                father[right[f]] = f;
            }
            left[i] = f;
        } else {
            left[f] = right[i];
            if (left[f] != 0) {
                father[left[f]] = f;
            }
            right[i] = f;
        }
        if (g != 0) {
            if (sonf == 1) {
                right[g] = i;
            } else {
                left[g] = i;
            }
        }
        father[f] = i;
        father[i] = g;
        up(f);
        up(i);
    }

    // 编号为i的节点提根，变成编号为goal的节点的儿子
    // 如果goal == 0，表示把编号为i的节点变成整棵树的头
    public static void splay(int i, int goal) {
        int f = father[i], g = father[f];
        while (f != goal) {
            if (g != goal) {
                if (lr(i) == lr(f)) {
                    rotate(f);
                } else {
                    rotate(i);
                }
            }
            rotate(i);
            f = father[i];
            g = father[f];
        }
        if (goal == 0) {
            head = i;
        }
    }

    // 构建一字型长链
    public static int build1(int l, int r) {
        int h = cnt + 1;
        for (int i = l, last = 0; i <= r; i++, last = cnt) {
            key[++cnt] = i;
            father[cnt] = last;
            left[cnt] = right[cnt] = 0;
            size[cnt] = r - i + 1;
            if (last != 0) {
                right[last] = cnt;
            }
        }
        return h;
    }

    // 构建之字形长链
    public static int build2(int l, int r, int fa) {
        if (l > r) {
            return 0;
        }
        key[++cnt] = l;
        father[cnt] = fa;
        left[cnt] = right[cnt] = 0;
        int h = cnt;
        if (l < r) {
            key[++cnt] = r;
            father[cnt] = h;
            left[cnt] = right[cnt] = 0;
            int c = cnt;
            right[h] = c;
            left[c] = build2(l + 1, r - 1, c);
            up(c);
        }
        up(h);
        return h;
    }

    // 返回以i为头的树的高度
    public static int height(int i) {
        if (i == 0) {
            return 0;
        }
        return Math.max(height(left[i]), height(right[i])) + 1;
    }

    public static void main(String[] args) {
        System.out.println("构建一字型长链");
        System.out.println("最下方节点执行splay，观察高度变化");
        head = build1(1, 1000);
        System.out.println("splay之前的链长度 : " + height(head));
        splay(cnt, 0);
        System.out.println("splay之后的链长度 : " + height(head));

        System.out.println("==================");

        System.out.println("构建之字型长链");
        System.out.println("最下方节点执行splay，观察高度变化");
        head = build2(1, 1000, 0);
        System.out.println("splay之前的链长度 : " + height(head));
        splay(cnt, 0);
        System.out.println("splay之后的链长度 : " + height(head));
    }

}
```
