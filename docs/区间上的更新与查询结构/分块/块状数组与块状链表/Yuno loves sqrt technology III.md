# Yuno loves sqrt technology III

## [P5048 [Ynoi2019 模拟赛] Yuno loves sqrt technology III](https://www.luogu.com.cn/problem/P5048)

> - **Question**
>   - 给定一个长度为 `n` 的数组 `arr` ，接下来有 `m` 条操作，每条操作格式如下，操作 `l r` ，打印 `arr[l ~ r]` 范围上，众数到底出现了几次。
>   - **Tips**
>     - `1 <= 所有数据 <= 5 * 10^5`

## Java

> - **Question**

```java
// freq[i][j]表示前i块中j出现的次数，这个结构不能用了，因为空间不允许
// sortList用来收集同一种数的下标列表
// listIdx[i] = j，表示arr[i]这个元素在sortList里的j位置
// modeCnt[i][j]表示从i块到j块中众数的出现次数
// 讨论左右散块中的数字，能否让众数的次数变得更大

// For Most Online Judge systems

import java.io.*;
import java.util.*;

public class Main {

    public static int MAXN = 500001;
    public static int MAXB = 801;
    public static int n, m;
    public static int[] arr = new int[MAXN];

    public static int blen, bnum;
    public static int[] bi = new int[MAXN];
    public static int[] bl = new int[MAXB];
    public static int[] br = new int[MAXB];

    // (值、下标)，用来收集同一种数的下标列表
    public static int[][] sortList = new int[MAXN][2];
    // listIdx[i] = j，表示arr[i]这个元素在sortList里的j位置
    public static int[] listIdx = new int[MAXN];
    // modeCnt[i][j]表示从i块到j块中众数的出现次数
    public static int[][] modeCnt = new int[MAXB][MAXB];
    // 数字词频统计
    public static int[] numCnt = new int[MAXN];

    public static void prepare() {
        blen = (int) Math.sqrt(n);
        bnum = (n + blen - 1) / blen;
        for (int i = 1; i <= n; i++) {
            bi[i] = (i - 1) / blen + 1;
        }
        for (int i = 1; i <= bnum; i++) {
            bl[i] = (i - 1) * blen + 1;
            br[i] = Math.min(i * blen, n);
        }
        for (int i = 1; i <= n; i++) {
            sortList[i][0] = arr[i];
            sortList[i][1] = i;
        }
        Arrays.sort(sortList, 1, n + 1, (a, b) -> a[0] != b[0] ? a[0] - b[0] : a[1] - b[1]);
        for (int i = 1; i <= n; i++) {
            listIdx[sortList[i][1]] = i;
        }
        for (int i = 1; i <= bnum; i++) {
            for (int j = i; j <= bnum; j++) {
                int cnt = modeCnt[i][j - 1];
                for (int k = bl[j]; k <= br[j]; k++) {
                    cnt = Math.max(cnt, ++numCnt[arr[k]]);
                }
                modeCnt[i][j] = cnt;
            }
            for (int j = 1; j <= n; j++) {
                numCnt[j] = 0;
            }
        }
    }

    public static int query(int l, int r) {
        int ans = 0;
        if (bi[l] == bi[r]) {
            for (int i = l; i <= r; i++) {
                ans = Math.max(ans, ++numCnt[arr[i]]);
            }
            for (int i = l; i <= r; i++) {
                numCnt[arr[i]] = 0;
            }
        } else {
            ans = modeCnt[bi[l] + 1][bi[r] - 1];
            for (int i = l, idx; i <= br[bi[l]]; i++) {
                idx = listIdx[i];
                while (idx + ans <= n && sortList[idx + ans][0] == arr[i] && sortList[idx + ans][1] <= r) {
                    ans++;
                }
            }
            for (int i = bl[bi[r]], idx; i <= r; i++) {
                idx = listIdx[i];
                while (idx - ans >= 1 && sortList[idx - ans][0] == arr[i] && sortList[idx - ans][1] >= l) {
                    ans++;
                }
            }
        }
        return ans;
    }

    public static void main(String[] args) throws IOException {
        FastReader in = new FastReader(System.in);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        n = in.nextInt();
        m = in.nextInt();
        for (int i = 1; i <= n; i++) {
            arr[i] = in.nextInt();
        }
        prepare();
        for (int i = 1, l, r, lastAns = 0; i <= m; i++) {
            l = in.nextInt() ^ lastAns;
            r = in.nextInt() ^ lastAns;
            lastAns = query(l, r);
            out.println(lastAns);
        }
        out.flush();
        out.close();
    }

    // 读写工具类
    static class FastReader {
        private final byte[] buffer = new byte[1 << 20];
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
