# Internet Bandwidth

## [Internet Bandwidth](https://lightoj.com/problem/internet-bandwidth)

> - ***Question***
>   - On the Internet, machines (nodes) are richly interconnected, and many paths may exist between a given pair of nodes. The total message-carrying capacity (bandwidth) between two given nodes is the maximal amount of data per unit time that can be transmitted from one node to the other. Using a technique called packet switching; this data can be transmitted along several paths at the same time.
>   - For example, the figure shows a network with four nodes (shown as circles), with a total of five connections among them. Every connection is labeled with a bandwidth that represents its data-carrying capacity per unit time.
>   - In our example, the bandwidth between node 1 and node 4 is 25, which might be thought of as the sum of the bandwidths 10 along the path 1-2-4, 10 along the path 1-3-4, and 5 along the path 1-2-3-4. No other combination of paths between nodes 1 and 4 provides a larger bandwidth.
>   - You must write a program that computes the bandwidth between two given nodes in a network, given the individual bandwidths of all the connections in the network. In this problem, assume that the bandwidth of a connection is always the same in both directions (which is not necessarily true in the real world).
>   - ***input:***
>     - Input starts with an integer T (≤ 30), denoting the number of test cases.
>     - Every description starts with a line containing an integer n (2 ≤ n ≤ 100), which is the number of nodes in the network. The nodes are numbered from 1 to n. The next line contains three numbers s, t, and c. The numbers s and t are the source and destination nodes, and the number c (c ≤ 5000, s ≠ t) is the total number of connections in the network. Following this are c lines describing the connections. Each of these lines contains three integers: the first two are the numbers of the connected nodes, and the third number is the bandwidth of the connection. The bandwidth is a non-negative number not greater than 1000.
>     - There might be more than one connection between a pair of nodes, but a node cannot be connected to itself. All connections are bi-directional, i.e. data can be transmitted in both directions along a connection, but the sum of the amount of data transmitted in both directions must be less than the bandwidth.
>   - ***ouput:***
>     - For each case of input, print the case number and the total bandwidth between the source node s and the destination node t.

---

## *Java*

> - ***Dinic***

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Scanner;

class Solution {
    
    public static class Edge {
        
        // 起点
        public int from;
        // 指向点
        public int to;
        // 管道剩余容量
        // from要到to需要剩余容量不为0
        public int available;
        
        public Edge(int a, int b, int c) {
            from = a;
            to = b;
            available = c;
        }
        
    }
    
    public static class Dinic {
        
        // 网络有多少个节点
        private final int N;
        // nexts[i]是一个列表，存储从第i个节点出去的边的索引号
        private final ArrayList<ArrayList<Integer>> nexts;
        // 边集
        // 从0号开始
        // i是偶数，是原图中的正向边，i+1是奇数，与i号边互为反向边
        // i号边权重为容量-流量，i+1号边权重为流量，因此边权是变动的
        // 给定一个编号，异或上1就是它的反向边号
        private final ArrayList<Edge> edges;
        // 高度数组，表示距离源点的远近（又不是客观的远近），是指在宽度优先遍历中的第几层（第几回合入队列），有可能一个节点和起点相连接，但由于容量为0导致它不在第一层，起点不能直接流向它，可能要通过其它节点到它
        private final int[] depth;
        // 表示当前遍历到的节点i，应该从边cur[i]开始遍历，即排除掉满了的管道
        // cur[i]存储的是nexts[i]中的索引而不是边集的索引
        private final int[] cur;
        
        public Dinic(int nums) {
            // 规避0-n-1和1-n
            N = nums + 1;
            // 从节点指出的边
            nexts = new ArrayList<>();
            for (int i = 0; i <= N; i++) {
                nexts.add(new ArrayList<>());
            }
            edges = new ArrayList<>();
            depth = new int[N];
            cur = new int[N];
        }
        
        public void addEdge(int u, int v, int r) {
            // 边集大小为m
            int m = edges.size();
            // 最开始流量为0，正向边为原边
            edges.add(new Edge(u, v, r));
            // 之前最后一个是m-1
            nexts.get(u).add(m);
            // 反向边权重等于流量等于0
            edges.add(new Edge(v, u, 0));
            // 紧挨正向边，索引号成一对
            nexts.get(v).add(m + 1);
        }
        
        public int maxFlow(int s, int t) {
            int flow = 0;
            // 如果s能够到t
            while (bfs(s, t)) {
                // 每个循环前都清零
                Arrays.fill(cur, 0);
                // 就算流量出来
                // 假设s有三条边出来，分别的容量为30、50、70
                // 那么我们认为s最开始接受到一个流量为无穷大的流体，然后根据它向外的管道进行30、50、70流量的分发
                flow += dfs(s, t, Integer.MAX_VALUE);
                // 清空，再来
                Arrays.fill(depth, 0);
            }
            // 返回流量
            return flow;
        }
        
        // 判断s流出的液体能不能到t
        private boolean bfs(int s, int t) {
            // 隐含的台词，s永远在0层，depth[s] = 0
            // 宽度优先的队列
            LinkedList<Integer> queue = new LinkedList<>();
            queue.addFirst(s);
            // 记录节点有没有被遍历过
            boolean[] visited = new boolean[N];
            visited[s] = true;
            while (!queue.isEmpty()) {
                // u是当前节点
                int u = queue.pollLast();
                for (int i = 0; i < nexts.get(u).size(); i++) {
                    // 拿出出边
                    Edge e = edges.get(nexts.get(u).get(i));
                    // 去下一个节点
                    int v = e.to;
                    // 下一个节点没来过且下一个节点的容量大于0，即能通过流体
                    if (!visited[v] && e.available > 0) {
                        // 记录你来过
                        visited[v] = true;
                        // 你的深度记录
                        depth[v] = depth[u] + 1;
                        if (v == t) {
                            // 你就是终点，退出
                            break;
                        }
                        // 否则入队
                        queue.addFirst(v);
                    }
                }
            }
            // 返回流体能不能到达终点
            return visited[t];
        }
        
        // 当前来到了s点，s可变
        // 最终目标是t，t固定参数
        // r，收到的任务
        // 收集到的流，作为结果返回，ans <= r
        private int dfs(int s, int t, int r) {
            if (s == t || r == 0) {
                // 已经流到终点了，终点收到的流量返回
                // 没任务了，返回空流量，即来到这个节点的管道剩余容量为0
                return r;
            }
            int f = 0;
            int flow = 0;
            // s点从s点向外的哪条边开始试 -> cur[s]
            // 试过了cur[s]++
            for (; cur[s] < nexts.get(s).size(); cur[s]++) {
                // 边集中边的边号
                int ei = nexts.get(s).get(cur[s]);
                // 正向边和反向边
                Edge e = edges.get(ei);
                Edge o = edges.get(ei ^ 1);
                // 如果我下一级的点高度比我高一个我才走，一个优化，避免回头路
                // 此外我往下走继续向终点进发，往下的流量为去下一个节点的管道的容量与收到的流量的最小值
                // 如果我下一级节点能到终点，即返回的流量不是0
                if (depth[e.to] == depth[s] + 1 && (f = dfs(e.to, t, Math.min(e.available, r))) != 0) {
                    // 我这条管道的剩余容量-流量
                    e.available -= f;
                    // 反向边容量增加，因为它是流量
                    o.available += f;
                    // 流量加上
                    flow += f;
                    // 这个节点的任务完成了一些，看看这个节点出发的路能不能够完成其他路
                    r -= f;
                    if (r <= 0) {
                        // 这个节点的任务完成了，可以返回了
                        break;
                    }
                }
            }
            // 返回流量
            return flow;
        }
        
    }
    
    public static void main(String[] args) {
        Scanner cin = new Scanner(System.in);
        int cases = cin.nextInt();
        for (int i = 1; i <= cases; i++) {
            int n = cin.nextInt();
            int s = cin.nextInt();
            int t = cin.nextInt();
            int m = cin.nextInt();
            Dinic dinic = new Dinic(n);
            for (int j = 0; j < m; j++) {
                int from = cin.nextInt();
                int to = cin.nextInt();
                int weight = cin.nextInt();
                // 这个算法为了既满足有向图又满足无向图，题目是无向图
                // 这里加两次是因为无向图是特殊的有向图，无向图的一条边转化为有向的权重相等的两条
                // 事实上加了4条边，因为每次都加两条
                dinic.addEdge(from, to, weight);
                // 如果题目是纯有向，删除下面这个即可
                dinic.addEdge(to, from, weight);
            }
            // 求最大网络流
            int ans = dinic.maxFlow(s, t);
            System.out.println("Case " + i + ": " + ans);
        }
        cin.close();
    }
    
}
```
