# Small Multiple

## [[ABC077D] Small Multiple](https://www.luogu.com.cn/problem/AT_arc084_b)

> - ***Question***
>   - 给定一个整数 `k` ，求一个k的正整数倍 `s` ，使得在十进制下， `s` 的数位累加和最小。
>   - ***输入描述***
>     - 一行一个正整数 `k` 。
>   - ***输出描述***
>     - 输出 `k` 的正整数倍的最小数位累加和。
>   - ***tips:***
>     - `2 <= k <= 10^5`

## Java

> - ***巧妙构造 + 01BFS算法实现同余最短路***

```java
import java.io.*;
import java.util.ArrayDeque;

public class Main {

    public static int MAXK = 100001;

    public static int k;

    // 01bfs需要
    public static ArrayDeque<int[]> deque = new ArrayDeque<>();

    public static boolean[] visit = new boolean[MAXK];

    // 01bfs
    public static int bfs() {
        deque.clear();
        deque.add(new int[]{1, 1});
        int[] cur;
        int mod, cost;
        while (!deque.isEmpty()) {
            cur = deque.pollFirst();
            mod = cur[0];
            cost = cur[1];
            if (!visit[mod]) {
                visit[mod] = true;
                if (mod == 0) {
                    return cost;
                }
                deque.addFirst(new int[]{(mod * 10) % k, cost});
                deque.addLast(new int[]{(mod + 1) % k, cost + 1});
            }
        }
        return -1;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        k = (int) in.nval;
        out.println(bfs());
        out.flush();
        out.close();
        br.close();
    }

}
```
