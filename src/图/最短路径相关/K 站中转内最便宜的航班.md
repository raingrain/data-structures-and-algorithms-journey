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

```java
class Solution {

    public static int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] cost = new int[n];
        Arrays.fill(cost, Integer.MAX_VALUE);
        cost[src] = 0;
        for (int i = 0; i <= k; i++) {
            // 拷贝当前层
            int[] next = Arrays.copyOf(cost, n);
            for (int[] f : flights) {
                // 如果当前层中这个城市能到达
                if (cost[f[0]] != Integer.MAX_VALUE) {
                    // 那么这个航班中的目的地就进下一级队列
                    next[f[1]] = Math.min(next[f[1]], cost[f[0]] + f[2]);
                }
            }
            cost = next;
        }
        return cost[dst] == Integer.MAX_VALUE ? -1 : cost[dst];
    }

}
```

---

> ***last change: 2023/5/8***

---
