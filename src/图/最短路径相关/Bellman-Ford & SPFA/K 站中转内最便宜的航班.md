# K 站中转内最便宜的航班

## [787. K 站中转内最便宜的航班](https://leetcode.cn/problems/cheapest-flights-within-k-stops/)

> - ***Question***
>   - 有 `n` 个城市通过一些航班连接。给你一个数组 `flights` ，其中 `flights[i] = [fromi, toi, pricei]` ，表示该航班都从城市 `fromi` 开始，以价格 `pricei` 抵达 `toi` 。
>   - 现在给定所有的城市和航班，以及出发城市 `src` 和目的地 `dst` ，你的任务是找到出一条最多经过 `k` 站中转的路线，使得从 `src` 到 `dst` 的价格最便宜，并返回该价格。如果不存在这样的路线，则输出 `-1` 。
>   - ***tips:***
>     - `1 <= n <= 100`
>     - `0 <= flights.length <= (n * (n - 1) / 2)`
>     - `flights[i].length == 3`
>     - `0 <= fromi, toi < n`
>     - `fromi != toi`
>     - `1 <= pricei <= 10^4`
>     - 航班没有重复，且不存在自环
>     - `0 <= src, dst, k < n`
>     - `src != dst`

---

## *Java*

> - ***Bellman Ford算法 + SPFA算法优化***
>   - 两种算法在图中有负权边的情况下能够正常运行，而Dijkstra不行。
>   - 很轻易就能发现，每一轮考察所有的边看看能否做松弛操作是不必要的。因为只有上一次被某条边松弛过的节点，所连接的边，才有可能引起下一次的松弛操作。所以用队列来维护这一轮哪些节点的 `distance` 变小了。下一轮只需要对这些点的所有边，考察有没有松弛操作即可。SPFA只优化了常数时间，在大多数情况下跑得很快，但时间复杂度为 `O(n * m)` 。看复杂度就知道只适用于小图，根据数据量谨慎使用，在没有负权边时要使用Dijkstra算法。
>   - 用途：适用于小图；解决有负边（没有负环）的图的单源最短路径问题；可以判断从某个点出发是否能遇到负环，如果想判断整张有向图有没有负环，需要设置虚拟源点（指向原图所有节点）；并行计算时会有很大优势，因为每一轮多点判断松弛操作是相互独立的，可以交给多线程处理。

```java
class Solution {

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        // 生成邻接表
        // 第一层的下标表示出发城市，它的元素是一个数组，里面存放着许多小数组，每个小数组为[能通过一张机票到达的城市, 机票价格]
        ArrayList<ArrayList<int[]>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            // 全部初始化
            graph.add(new ArrayList<>());
        }
        for (int[] flight : flights) {
            // 找到一个加一个
            graph.get(flight[0]).add(new int[]{flight[1], flight[2]});
        }
        // cost[i]表示从起点到当前城市的最小花费
        int[] cost = new int[n];
        // 最开始都到达不了
        Arrays.fill(cost, Integer.MAX_VALUE);
        // 自己到自己价格为0
        cost[src] = 0;
        // 优化！
        // 松弛操作必定只会发生在最短路径前导节点松弛成功过的节点上，用一个队列记录松弛过的节点，可以避免了冗余计算。
        // 用哈希表替代队列去重，我们希望每一层遍历时每一个城市只出现一次
        // key是城市，value是起点到当前点的距离
        HashMap<Integer, Integer> curMap = new HashMap<>();
        curMap.put(src, 0);
        // 题目要求最多k次中转
        // 事实上这里最多为n-1，因为一个拥有n个节点的图最多有n-1条边，假设所有节点连成类似于链表的结构，起点和终点分别在首尾两侧，最坏情况下遍历n-1次也可以拿到正确操作，即最短路径所经过的边不会超过n-1条边
        // 这种操作是对相邻节点的访问，也叫做松弛
        for (int i = 0; i <= k; i++) {
            // 下次遍历哪些城市
            HashMap<Integer, Integer> nextMap = new HashMap<>();
            // 弹出队列中的元素
            for (Map.Entry<Integer, Integer> entry : curMap.entrySet()) {
                // 当前城市和价格提取出来
                int curCity = entry.getKey();
                int curCost = entry.getValue();
                // 看看这个城市能去哪些城市
                for (int[] flight : graph.get(curCity)) {
                    int nextCity = flight[0];
                    int nextCost = flight[1];
                    // 优化！
                    // 只有来过nextCity且从起点城市到nextCity的代价小于之前的代价的时候，我们才进行松弛操作（即有必要更新最短花费）
                    // 注意第一次来nextCity时cost[nextCity]为系统最大值，它一定会进下面的if，我们不用额外判断nextCity有没有来过
                    if (curCost + nextCost < cost[nextCity]) {
                        cost[nextCity] = curCost + nextCost;
                        nextMap.put(nextCity, curCost + nextCost);
                    }
                    /*// 更新代价表中的记录
                    cost[nextCity] = Math.min(cost[nextCity], curCost + nextCost);
                    // 注意这里加入下一级队列时要比较，因为当前层可能有多个元素要进下一个队列，但我们又希望一个城市在队列里面只出现一次
                    // 这时候我们肯定选择起点到当前城市代价最小的，如果下一级队列中已经有这个城市了，我们更新它的代价，没有就新建
                    nextMap.put(nextCity, Math.min(nextMap.getOrDefault(nextCity, Integer.MAX_VALUE), curCost + nextCost));*/
                }
            }
            // 复用
            curMap = nextMap;
        }
        // 这时候已经找出最短路径了（假设遍历n-1次）
        // 但是由于负权环可以无限制的降低总花费，所以如果我们再进行一次遍历，若发现第n次操作仍可降低花销，就一定存在负权环
        // 或者统计每一个节点入队列的次数，如果一个节点入队列n次，则说明存在负环
        // 统计起点到每一个点的最短路径中所包含的边数，如果边数大于等于n，也存在负环。即遍历时多准备一个数组cnt记录起点到当前点的最短路边数，只要它能再走n步使得cnt[i]>=0即表示图中一定有负环，但注意负环不一定从起点开始，最开始的时候需要把所有点都入队而不是只加入起点
        // 判断可不可达
        return cost[dst] == Integer.MAX_VALUE ? -1 : cost[dst];
    }

}
```

> - ***Bellman Ford算法原版***
>   - 这个算法每次都松弛所有边，有点冗余。
>   - 松弛操作是指假设源点为 `A` ，从 `A` 到任意点 `F` 的最短距离为 `distance[F]` ，假设从点 `P` 出发某条边，去往点 `S` ，边权为 `W` ，如果发现 `distance[P] + W < distance[S]` ，也就是通过该边可以让 `distance[S]` 变小，那么就说 `P` 出发的这条边对点 `S` 进行了松弛操作。
>   - 该算法即每一轮考察每条边，每条边都尝试松弛操作，那么若干点的 `distance` 会变小，当某一轮发现不再有松弛操作出现时算法停止。
>   - 假设点的数量为 `N` ，边的数量为 `M` ，每一轮时间复杂度 `O(M)` 。最短路存在的情况下，因为 `1` 次松弛操作会使 `1` 个点的最短路的边数 `+1` ，而从源点出发到任何点的最短路最多走过全部的 `n` 个点，所以松弛的轮数必然 `<= n - 1` ，所以Bellman-Ford算法时间复杂度 `O(M * N)` 。
>   - 重要推广：判断从某个点出发能不能到达负环。上面已经说了，如果从 `A` 出发存在最短路（没有负环），那么松弛的轮数必然 `<= n - 1` 而如果从 `A` 点出发到达一个负环，那么松弛操作显然会无休止地进行下去。所以，如果发现从 `A` 点出发，在第 `n` 轮时松弛操作依然存在，说明从 `A` 点出发能够到达一个负环。

```java
class Solution {

    public static int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] cost = new int[n];
        Arrays.fill(cost, Integer.MAX_VALUE);
        cost[src] = 0;
        // 我们期望松弛次数等于中转次数
        for (int i = 0; i <= k; i++) {
            // 拷贝当前层
            int[] next = Arrays.copyOf(cost, n);
            for (int[] f : flights) {
                // 如果当前层中这个城市能到达
                if (cost[f[0]] != Integer.MAX_VALUE) {
                    // 那么这个航班中的目的地就进下一级队列
                    // cost[f[0]]实际应该是next[f[0]]，使用老表是为了题目要求
                    next[f[1]] = Math.min(next[f[1]], cost[f[0]] + f[2]);
                }
            }
            cost = next;
        }
        return cost[dst] == Integer.MAX_VALUE ? -1 : cost[dst];
    }

}
```
