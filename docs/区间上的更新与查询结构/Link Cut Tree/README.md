# Link Cut Tree

## 动态森林问题

一片森林中不断加入或删除边，期间保证图仍然是一片森林，维护连通性、路径、动态LCA、动态生成树等问题，被称为动态树问题，解决动态树问题的途径有很多，比如有些题目，可以用线段树分治等离线方式处理，LCT可以在线维护动态森林，也是算法竞赛中最经典、最常用的动态树数据结构，全局平衡二叉树、Euler Tour Tree、Top Tree，也能解决部分动态树的问题，但是普及度不如LCT。

## LCT的原理

0. 务必已经掌握splay树的提根操作，包括rotate方法、splay方法的实现
1. 实链和辅助splay树的概念，实链的从上到下，对应splay树的中序遍历
2. 辅助splay树节点的fa、认父不认子的设计、顶部节点的判断
3. 辅助splay树节点的定制信息，根据不同题目进行不同定制
4. access，建立实链
5. makeroot，换顶，需要结合翻转操作
6. findroot
7. split
8. link
9. cut

## LCT的复杂度

1. 每个操作单次均摊代价 `O(log n)` ，但是常数时间比较大
2. splay的势能分析
3. 发生每种操作，都会先调用access串连好实链，然后再执行操作，以此保证性能
4. 某一次access的代价可能很高，但是整条实链会调整成splay树，方便以后的访问
5. 老实链因为新实链而断开，各个部分依然是splay树的组织，各个部分保留性能优势
6. 此处实链上产生断点，增加了代价，别处会串联其他实链，增加了便利性，补偿了代价

## [P3690 【模板】动态树（LCT）](https://www.luogu.com.cn/problem/P3690)

> - **Question**
>   - 一共 `n` 个点，每个点给定点权，一共 `m` 条操作，操作类型如下：
>     - 操作 `0 x y` ：查询点 `x` 到点 `y` 的路径上，所有点权的异或和，保证 `x` 和 `y` 连通，
>     - 操作 `1 x y` ：点 `x` 和点 `y` 之间增加直接边，如果 `x` 和 `y` 已经连通则忽略，
>     - 操作 `2 x y` ：删除点 `x` 和点 `y` 之间的直接边，如果不存在这条直接边则忽略，
>     - 操作 `3 x y` ：点 `x` 的点权变成 `y` 。
>   - **Tips**
>     - `1 <= n <= 10^5`
>     - `1 <= m <= 3 * 10^5`

## Java

> - **Question: LCT模版题**

```java
// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 100001;
    public static int n, m;
    public static int[] arr = new int[MAXN];

    // 既保存辅助splay内，每个节点的父节点
    // 也保存沿虚边向上跳到的原树父节点，认父不认子
    public static int[] fa = new int[MAXN];
    public static int[] ls = new int[MAXN];
    public static int[] rs = new int[MAXN];

    // 翻转操作的懒更新标记
    public static boolean[] rev = new boolean[MAXN];

    // 提根之前，先处理沿途的翻转标记，准备一个栈记录沿途节点
    public static int[] sta = new int[MAXN];

    // 本题需要异或和
    public static int[] xorsum = new int[MAXN];

    // 不同题目实现不同的up方法
    public static void up(int x) {
        xorsum[x] = xorsum[ls[x]] ^ xorsum[rs[x]] ^ arr[x];
    }

    // 判断节点x是不是辅助splay的顶部节点
    public static boolean isroot(int x) {
        return ls[fa[x]] != x && rs[fa[x]] != x;
    }

    // x不是辅助splay的顶部节点才能调用，返回x是其父的哪侧儿子
    public static int lr(int x) {
        return ls[fa[x]] == x ? 0 : 1;
    }

    // 翻转以x为根的辅助splay，交换左右儿子，打上翻转标记
    public static void reverse(int x) {
        if (x != 0) {
            int tmp = ls[x];
            ls[x] = rs[x];
            rs[x] = tmp;
            rev[x] = !rev[x];
        }
    }

    // 处理翻转懒更新
    public static void down(int x) {
        if (rev[x]) {
            reverse(ls[x]);
            reverse(rs[x]);
            rev[x] = false;
        }
    }

    // x向上旋转
    public static void rotate(int x) {
        int f = fa[x], g = fa[f];
        if (lr(x) == 0) {
            ls[f] = rs[x];
            if (ls[f] != 0) {
                fa[ls[f]] = f;
            }
            rs[x] = f;
        } else {
            rs[f] = ls[x];
            if (rs[f] != 0) {
                fa[rs[f]] = f;
            }
            ls[x] = f;
        }
        if (!isroot(f)) {
            if (lr(f) == 0) {
                ls[g] = x;
            } else {
                rs[g] = x;
            }
        }
        fa[f] = x;
        fa[x] = g;
        up(f);
        up(x);
    }

    // x提到辅助splay的顶部
    public static void splay(int x) {
        int siz = 0;
        sta[++siz] = x;
        for (int y = x; !isroot(y); y = fa[y]) {
            sta[++siz] = fa[y];
        }
        while (siz != 0) {
            down(sta[siz--]);
        }
        while (!isroot(x)) {
            int f = fa[x];
            if (!isroot(f)) {
                if (lr(x) == lr(f)) {
                    rotate(f);
                } else {
                    rotate(x);
                }
            }
            rotate(x);
        }
    }

    // 打通当前原树根到x的路径，使其成为一条实链
    public static void access(int x) {
        for (int y = 0; x != 0; y = x, x = fa[x]) {
            splay(x);
            rs[x] = y;
            up(x);
        }
    }

    // 让x变成原树的根，不改变连通方式
    public static void makeroot(int x) {
        access(x);
        splay(x);
        reverse(x);
    }

    // 建立根到x的实链，根变成辅助splay的顶，返回x所在原树的根
    public static int findroot(int x) {
        access(x);
        splay(x);
        down(x);
        while (ls[x] != 0) {
            x = ls[x];
            down(x);
        }
        splay(x);
        return x;
    }

    // 点x到点y的路径暴露出来，这条路径成为一条实链，组成一棵以y为顶部的辅助splay树
    public static void split(int x, int y) {
        makeroot(x);
        access(y);
        splay(y);
    }

    // 原树中连接x和y，如果原本连通则忽略
    public static void link(int x, int y) {
        makeroot(x);
        if (findroot(y) != x) {
            fa[x] = y;
        }
    }

    // 原树中切断x和y之间的直接边，没有直接边则忽略
    public static void cut(int x, int y) {
        makeroot(x);
        if (findroot(y) == x && fa[y] == x && ls[y] == 0 && rs[x] == y) {
            fa[y] = rs[x] = 0;
            up(x);
        }
    }

    public static void main(String[] args) throws Exception {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
            xorsum[i] = arr[i];
        }
        for (int i = 1, op, x, y; i <= m; i++) {
            op = in.nextInt();
            x = in.nextInt();
            y = in.nextInt();
            if (op == 0) {
                split(x, y);
                out.println(xorsum[y]);
            } else if (op == 1) {
                link(x, y);
            } else if (op == 2) {
                cut(x, y);
            } else {
                splay(x);
                arr[x] = y;
                up(x);
            }
        }
        out.flush();
        out.close();
    }

    // 读写工具类
    static class FastReader {

        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;
        private final InputStream in;

        FastReader(InputStream in) {
            this.in = in;
        }

        private int readByte() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0)
                    return -1;
            }
            return buffer[ptr++];
        }

        int nextInt() throws IOException {
            int c;
            do {
                c = readByte();
            } while (c <= ' ' && c != -1);
            boolean neg = false;
            if (c == '-') {
                neg = true;
                c = readByte();
            }
            int val = 0;
            while (c > ' ' && c != -1) {
                val = val * 10 + (c - '0');
                c = readByte();
            }
            return neg ? -val : val;
        }

    }

}
```
