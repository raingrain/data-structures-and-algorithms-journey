# A星算法

## 左老师课堂讲述

> - ***Question***
>   - 给定一个矩形区域，区域中每一个格子的值表示从上下左右四个方向到达该点所花费的代价，给定起点和终点位置，返回最小代价和。
>   - 区域值为 `0` 表示障碍。

---

## *Java*

> - ***用Dijkstra检验A星算法***
>   - 我们知道在Dijkstra中，小根堆的排序策略是起点到当前点的最短路径，但我们是要找到到目标点的最短路径的，Dijkstra反而有点像宽度优先遍历一层层往外扩散直到找到目标点，不具有引导性。
>   - 我们在排序时加上一个值，这个值叫做当前点到目标点的估算距离，这个距离可以是曼哈顿距离、欧式距离等。与Dijkstra相比，A星算法每个点存入堆时多记录这一条数据，排序的依据则是起点到当前点的最短路径加上估算距离的和。
>   - A星算法只做了上述改动，但却是遍历过程具备了引导性，这样堆会先弹出实际情况下离终点近的点进行遍历。
>   - 注意，估算距离一定要小于当前点到终点的实际距离。估算距离越小，需要计算的节点越多，估算距离越大，引导性越强。比如如果知道所有代价最小值大于 `100` ，估算距离可以使用曼哈顿距离乘以 `10` 。
>   - Dijkstra是一种特殊的A星算法，即估算距离为 `0` 的A星算法。

```java
class Solution {

    // 对数器验证
    private static int dijkstra(int[][] map, int startRow, int startCol, int targetRow, int targetCol) {
        if (map[startRow][startCol] == 0 || map[targetRow][targetCol] == 0) {
            return Integer.MIN_VALUE;
        }
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(o -> o[2]));
        boolean[][] isVisited = new boolean[map.length][map[0].length];
        minHeap.offer(new int[]{startRow, startCol, map[startRow][startCol]});
        int ans = Integer.MIN_VALUE;
        while (!minHeap.isEmpty()) {
            int[] cur = minHeap.poll();
            int curRow = cur[0];
            int curCol = cur[1];
            int distance = cur[2];
            if (isVisited[curRow][curCol]) {
                continue;
            }
            isVisited[curRow][curCol] = true;
            if (curRow == targetRow && curCol == targetCol) {
                ans = distance;
                break;
            }
            addInDijkstra(distance, curRow - 1, curCol, map, isVisited, minHeap);
            addInDijkstra(distance, curRow + 1, curCol, map, isVisited, minHeap);
            addInDijkstra(distance, curRow, curCol - 1, map, isVisited, minHeap);
            addInDijkstra(distance, curRow, curCol + 1, map, isVisited, minHeap);
        }
        return ans;
    }

    private static void addInDijkstra(int preDistance, int curRow, int curCol, int[][] map, boolean[][] isVisited, PriorityQueue<int[]> minHeap) {
        if (curRow >= 0 && curRow < map.length && curCol >= 0 && curCol < map[0].length && map[curRow][curCol] != 0 && !isVisited[curRow][curCol]) {
            minHeap.offer(new int[]{curRow, curCol, preDistance + map[curRow][curCol]});
        }
    }

    // A*模板，两者代码差异很小
    private static int AStar(int[][] map, int startRow, int startCol, int targetRow, int targetCol) {
        if (map[startRow][startCol] == 0 || map[targetRow][targetCol] == 0) {
            return Integer.MIN_VALUE;
        }
        // 比较依据发生变化
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(o -> (o[2] + o[3])));
        boolean[][] isVisited = new boolean[map.length][map[0].length];
        minHeap.offer(new int[]{startRow, startCol, map[startRow][startCol], getManhattanDistance(startRow, startCol, targetRow, targetCol)});
        int ans = Integer.MIN_VALUE;
        while (!minHeap.isEmpty()) {
            int[] cur = minHeap.poll();
            int curRow = cur[0];
            int curCol = cur[1];
            int distance = cur[2];
            if (isVisited[curRow][curCol]) {
                continue;
            }
            isVisited[curRow][curCol] = true;
            if (curRow == targetRow && curCol == targetCol) {
                ans = distance;
                break;
            }
            addInAStar(distance, curRow - 1, curCol, targetRow, targetCol, map, isVisited, minHeap);
            addInAStar(distance, curRow + 1, curCol, targetRow, targetCol, map, isVisited, minHeap);
            addInAStar(distance, curRow, curCol + 1, targetRow, targetCol, map, isVisited, minHeap);
            addInAStar(distance, curRow, curCol - 1, targetRow, targetCol, map, isVisited, minHeap);
        }
        return ans;
    }

    private static void addInAStar(int preDistance, int curRow, int curCol, int targetRow, int targetCol, int[][] map, boolean[][] isVisited, PriorityQueue<int[]> minHeap) {
        if (curRow >= 0 && curRow < map.length && curCol >= 0 && curCol < map[0].length && map[curRow][curCol] != 0 && !isVisited[curRow][curCol]) {
            minHeap.offer(new int[]{curRow, curCol, preDistance + map[curRow][curCol], getManhattanDistance(curRow, curCol, targetRow, targetCol)});
        }
    }

    private static int getManhattanDistance(int curRow, int curCol, int targetRow, int targetCol) {
        // 获得曼哈顿距离，即x和y距离和
        return Math.abs(targetRow - curRow) + Math.abs(targetCol - curCol);
    }

    // 为了测试
    // map[i][j] == 0 代表障碍
    // map[i][j] > 0 代表通行代价
    public static int[][] randomMap(int len, int value) {
        int[][] ans = new int[len][len];
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                if (Math.random() < 0.2) {
                    ans[i][j] = 0;
                } else {
                    ans[i][j] = (int) (Math.random() * value);
                }
            }
        }
        return ans;
    }

    // 为了测试
    public static void printMap(int[][] map) {
        for (int i = 0; i < map.length; i++) {
            for (int j = 0; j < map[0].length; j++) {
                System.out.print((map[i][j] == 0 ? "X" : map[i][j]) + " ");
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        int len = 100;
        int value = 50;
        int testTime = 10000;
        System.out.println("功能测试开始");
        for (int i = 0; i < testTime; i++) {
            int n = (int) (Math.random() * len) + 2;
            int[][] map = randomMap(n, value);
            int startX = (int) (Math.random() * n);
            int startY = (int) (Math.random() * n);
            int targetX = (int) (Math.random() * n);
            int targetY = (int) (Math.random() * n);
            int ans1 = dijkstra(map, startX, startY, targetX, targetY);
            int ans2 = AStar(map, startX, startY, targetX, targetY);
            if (ans1 != ans2) {
                System.out.println("出错了!");
                printMap(map);
                System.out.println(startX + "," + startY);
                System.out.println(targetX + "," + targetY);
                System.out.println(ans1);
                System.out.println(ans2);
                break;
            }
        }
        System.out.println("功能测试结束");
    }

}
```

---

> ***last change: 2023/5/7***

---
