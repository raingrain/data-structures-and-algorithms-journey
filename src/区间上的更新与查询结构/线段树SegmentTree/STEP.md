# STEP

## [P6492 [COCI2010-2011#6] STEP](https://www.luogu.com.cn/problem/P6492)

> - ***Question***
>   - 给定一个长度为 `n` 的字符串，一开始字符串中全是'L'字符。有 `q` 次修改，每次指定一个位置 `i` ：如果 `i` 位置是'L'字符那么改成'R'字符；如果 `i` 位置是'R'字符那么改成'L'字符。如果一个子串是两种字符不停交替出现的样子，那么说这个子串是有效子串。每次修改后，都打印当前整个字符串中最长交替子串的长度

---

## *Java*

> - ***线段树解决区间合并***

```java
import java.io.*;

public class Main {

    public static int MAXN = 200001;

    // 原始数组
    public static int[] arr = new int[MAXN];

    // 交替最长子串长度
    public static int[] len = new int[MAXN << 2];

    // 交替最长前缀长度
    public static int[] pre = new int[MAXN << 2];

    // 交替最长后缀长度
    public static int[] suf = new int[MAXN << 2];

    public static void up(int l, int r, int i) {
        len[i] = Math.max(len[i << 1], len[i << 1 | 1]);
        pre[i] = pre[i << 1];
        suf[i] = suf[i << 1 | 1];
        int mid = (l + r) >> 1;
        int ln = mid - l + 1;
        int rn = r - mid;
        if (arr[mid] != arr[mid + 1]) {
            len[i] = Math.max(len[i], suf[i << 1] + pre[i << 1 | 1]);
            if (len[i << 1] == ln) {
                pre[i] = ln + pre[i << 1 | 1];
            }
            if (len[i << 1 | 1] == rn) {
                suf[i] = rn + suf[i << 1];
            }
        }
    }

    public static void build(int l, int r, int i) {
        if (l == r) {
            len[i] = 1;
            pre[i] = 1;
            suf[i] = 1;
        } else {
            int mid = (l + r) >> 1;
            build(l, mid, i << 1);
            build(mid + 1, r, i << 1 | 1);
            up(l, r, i);
        }
    }

    public static void reverse(int jobi, int l, int r, int i) {
        if (l == r) {
            arr[jobi] ^= 1;
        } else {
            int mid = (l + r) >> 1;
            if (jobi <= mid) {
                reverse(jobi, l, mid, i << 1);
            } else {
                reverse(jobi, mid + 1, r, i << 1 | 1);
            }
            up(l, r, i);
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        in.nextToken();
        int q = (int) in.nval;
        build(1, n, 1);
        for (int i = 1, index; i <= q; i++) {
            in.nextToken();
            index = (int) in.nval;
            reverse(index, 1, n, 1);
            out.println(len[1]);
        }
        out.flush();
        out.close();
        br.close();
    }

}
```
