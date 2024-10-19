# 不使用加强堆的Dijkstra算法

## 左老师课堂讲述

> - ***Question***
>   - 给定邻接表，生成指定城市之间的最短距离。

## Java

> - ***使用普通堆的Dijkstra***
>   - 更新的记录不直接修改原来的，而是往堆里面加入新记录，结算过的用一张布尔表记录，后续堆里再弹出结算完的就不管了，直接扔掉即可，即用忽略机制代替加强堆的原地修改。
>   - 如果要求返回最短路径呢？我们需要额外记录堆中当前城市是哪一个城市来的，弹出锁定时在外面维护一个 `fatherMap` ，弹出锁定时更新 `fatherMap` ，最后遍历这张表，从目标城市开始遍历即可，遍历时记录路径返回。

```java
class Solution {
    
    // 暴力方法
    // dfs尝试所有情况
    // 没有优化，就是纯暴力
    public static int minDistance1(int n, int[][] roads, int x, int y) {
        // 第一步生成邻接矩阵
        int[][] map = new int[n + 1][n + 1];
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= n; j++) {
                map[i][j] = Integer.MAX_VALUE;
            }
        }
        for (int[] road : roads) {
            map[road[0]][road[1]] = Math.min(map[road[0]][road[1]], road[2]);
            map[road[1]][road[0]] = Math.min(map[road[1]][road[0]], road[2]);
        }
        boolean[] visited = new boolean[n + 1];
        return process(x, y, n, map, visited);
    }
    
    // 当前来到的城市是cur，最终目的地是aim，一共有1~n这些城市
    // 所有城市之间的距离都在map里
    // 之前已经走过了哪些城市都记录在了visited里面，请不要重复经过
    // 返回从cur到aim所有可能的路里，最小距离是多少
    public static int process(int cur, int aim, int n, int[][] map, boolean[] visited) {
        if (visited[cur]) {
            return Integer.MAX_VALUE;
        }
        if (cur == aim) {
            return 0;
        }
        visited[cur] = true;
        int ans = Integer.MAX_VALUE;
        for (int next = 1; next <= n; next++) {
            if (next != cur && map[cur][next] != Integer.MAX_VALUE) {
                int rest = process(next, aim, n, map, visited);
                if (rest != Integer.MAX_VALUE) {
                    ans = Math.min(ans, map[cur][next] + rest);
                }
            }
        }
        visited[cur] = false;
        return ans;
    }
    
    // Dijkstra的解
    // n是城市数量
    // 城市编号：1 ~ n 0弃而不用
    // 返回从x到y所需的最小代价
    public static int minDistance2(int n, int[][] roads, int x, int y) {
        // 第一步生成邻接矩阵
        int[][] map = new int[n + 1][n + 1];
        for (int i = 0; i <= n; i++) {
            for (int j = 0; j <= n; j++) {
                map[i][j] = Integer.MAX_VALUE;
            }
        }
        // 建立路！
        for (int[] road : roads) {
            // 为什么要比较大小
            // 因为题目可能这样给数据
            // [1, 5, 10]
            // [1, 5, 7]
            map[road[0]][road[1]] = Math.min(map[road[0]][road[1]], road[2]);
            map[road[1]][road[0]] = Math.min(map[road[1]][road[0]], road[2]);
        }
        // computed[i] = true，表示从源出发点到i这个城市，已经计算出最短距离了
        // computed[i] = false，表示从源出发点到i这个城市，还没有计算出最短距离
        boolean[] computed = new boolean[n + 1];
        // 距离小根堆
        PriorityQueue<Node> minHeap = new PriorityQueue<>(Comparator.comparingInt(o1 -> o1.pathSum));
        // 起点到自己距离为0
        minHeap.add(new Node(x, 0));
        while (!minHeap.isEmpty()) {
            Node cur = minHeap.poll();
            if (computed[cur.city]) {
                // 当前城市来到过，直接弹出堆
                continue;
            }
            if (cur.city == y) {
                // 已经来到了终点
                return cur.pathSum;
            }
            // 锁定
            computed[cur.city] = true;
            // 遍历所有城市
            for (int next = 1; next <= n; next++) {
                // 不是当前城市，能到达的，没来过的，就更新记录，直接把一个新节点入堆
                if (next != cur.city && map[cur.city][next] != Integer.MAX_VALUE && !computed[next]) {
                    minHeap.offer(new Node(next, cur.pathSum + map[cur.city][next]));
                }
            }
        }
        return Integer.MAX_VALUE;
    }
    
    // 当前来到的Node，注意这不是城市的意思，这是就是一个普通的封装类
    // Node封装了，当前来到的城市是什么，以及，从源出发点到这个城市的路径和是多少？
    private static class Node {
        
        // 当前来到的城市编号
        public int city;
        // 从源出发点到这个城市的路径和
        public int pathSum;
        
        public Node(int city, int pathSum) {
            this.city = city;
            this.pathSum = pathSum;
        }
        
    }
    
    // 为了测试
    // 城市1~n
    // 随机生成m条道路
    // 每一条路的距离，在1~v之间
    public static int[][] randomRoads(int n, int m, int v) {
        int[][] roads = new int[m][3];
        for (int i = 0; i < m; i++) {
            int from = (int) (Math.random() * n) + 1;
            int to = (int) (Math.random() * n) + 1;
            int distance = (int) (Math.random() * v) + 1;
            roads[i] = new int[]{from, to, distance};
        }
        return roads;
    }
    
    // 为了测试
    public static void main(String[] args) {
        // 城市数量n，下标从1开始，不从0开始
        int n = 4;
        // 边的数量m，m的值不能大于n * (n-1) / 2
        int m = 4;
        // 所的路有m条
        // [a,b,c]表示a和b之间有路，距离为3，根据题意，本题中的边都是无向边
        // 假设有两条路
        // [1,3,7]，这条路是从1到3，距离是7
        // [1,3,4]，这条路是从1到3，距离是4
        // 那么应该忽略[1,3,7]，因为[1,3,4]比它好
        int[][] roads = new int[m][3];
        roads[0] = new int[]{1, 2, 4};
        roads[1] = new int[]{1, 3, 1};
        roads[2] = new int[]{1, 4, 1};
        roads[3] = new int[]{2, 3, 1};
        // 求从x到y的最短距离是多少，x和y应该在[1,n]之间
        int x = 2;
        int y = 4;
        
        // 暴力方法的解
        System.out.println(minDistance1(n, roads, x, y));
        
        // Dijkstra的解
        System.out.println(minDistance2(n, roads, x, y));
        
        // 下面开始随机验证
        int cityMaxSize = 12;
        int pathMax = 30;
        int testTimes = 20000;
        System.out.println("测试开始");
        for (int i = 0; i < testTimes; i++) {
            n = (int) (Math.random() * cityMaxSize) + 1;
            m = (int) (Math.random() * n * (n - 1) / 2) + 1;
            roads = randomRoads(n, m, pathMax);
            x = (int) (Math.random() * n) + 1;
            y = (int) (Math.random() * n) + 1;
            int ans1 = minDistance1(n, roads, x, y);
            int ans2 = minDistance2(n, roads, x, y);
            if (ans1 != ans2) {
                System.out.println("出错了！");
            }
        }
        System.out.println("测试结束");
    }
    
}
```
