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

// 新版本模版
public class Main {

    // 0:上，1:右，2:下，3:左
    public static int[] move = new int[]{-1, 0, 1, 0, -1};

    // Dijkstra算法
    // grid[i][j] == 0 代表障碍
    // grid[i][j] == 1 代表道路
    // 只能走上、下、左、右，不包括斜线方向
    // 返回从(startX, startY)到(targetX, targetY)的最短距离
    public static int minDistance1(int[][] grid, int startX, int startY, int targetX, int targetY) {
        if (grid[startX][startY] == 0 || grid[targetX][targetY] == 0) {
            return -1;
        }
        int n = grid.length;
        int m = grid[0].length;
        int[][] distance = new int[n][m];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
        distance[startX][startY] = 1;
        boolean[][] visited = new boolean[n][m];
        PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        // 0 : 行
        // 1 : 列
        // 2 : 从源点出发到达当前点的距离
        heap.add(new int[]{startX, startY, 1});
        while (!heap.isEmpty()) {
            int[] cur = heap.poll();
            int x = cur[0];
            int y = cur[1];
            if (visited[x][y]) {
                continue;
            }
            visited[x][y] = true;
            if (x == targetX && y == targetY) {
                return distance[x][y];
            }
            for (int i = 0, nx, ny; i < 4; i++) {
                nx = x + move[i];
                ny = y + move[i + 1];
                if (nx >= 0 && nx < n && ny >= 0 && ny < m && grid[nx][ny] == 1 && !visited[nx][ny]
                        && distance[x][y] + 1 < distance[nx][ny]) {
                    distance[nx][ny] = distance[x][y] + 1;
                    heap.add(new int[]{nx, ny, distance[x][y] + 1});
                }
            }
        }
        return -1;
    }

    // A*算法
    // grid[i][j] == 0 代表障碍
    // grid[i][j] == 1 代表道路
    // 只能走上、下、左、右，不包括斜线方向
    // 返回从(startX, startY)到(targetX, targetY)的最短距离
    public static int minDistance2(int[][] grid, int startX, int startY, int targetX, int targetY) {
        if (grid[startX][startY] == 0 || grid[targetX][targetY] == 0) {
            return -1;
        }
        int n = grid.length;
        int m = grid[0].length;
        int[][] distance = new int[n][m];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                distance[i][j] = Integer.MAX_VALUE;
            }
        }
        distance[startX][startY] = 1;
        boolean[][] visited = new boolean[n][m];
        // 0 : 行
        // 1 : 列
        // 2 : 从源点出发到达当前点的距离 + 当前点到终点的预估距离
        PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[2] - b[2]);
        heap.add(new int[]{startX, startY, 1 + f1(startX, startY, targetX, targetY)});
        while (!heap.isEmpty()) {
            int[] cur = heap.poll();
            int x = cur[0];
            int y = cur[1];
            if (visited[x][y]) {
                continue;
            }
            visited[x][y] = true;
            if (x == targetX && y == targetY) {
                return distance[x][y];
            }
            for (int i = 0, nx, ny; i < 4; i++) {
                nx = x + move[i];
                ny = y + move[i + 1];
                if (nx >= 0 && nx < n && ny >= 0 && ny < m && grid[nx][ny] == 1 && !visited[nx][ny]
                        && distance[x][y] + 1 < distance[nx][ny]) {
                    distance[nx][ny] = distance[x][y] + 1;
                    heap.add(new int[]{nx, ny, distance[x][y] + 1 + f1(nx, ny, targetX, targetY)});
                }
            }
        }
        return -1;
    }

    // 曼哈顿距离
    public static int f1(int x, int y, int targetX, int targetY) {
        return (Math.abs(targetX - x) + Math.abs(targetY - y)) + 100;
    }

    // 对角线距离
    public static int f2(int x, int y, int targetX, int targetY) {
        return Math.max(Math.abs(targetX - x), Math.abs(targetY - y));
    }

    // 欧式距离
    public static double f3(int x, int y, int targetX, int targetY) {
        return Math.sqrt(Math.pow(targetX - x, 2) + Math.pow(targetY - y, 2));
    }

    // 为了测试
    public static int[][] randomGrid(int n) {
        int[][] grid = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (Math.random() < 0.3) {
                    // 每个格子有30%概率是0
                    grid[i][j] = 0;
                } else {
                    // 每个格子有70%概率是1
                    grid[i][j] = 1;
                }
            }
        }
        return grid;
    }

    // 为了测试
    public static void main(String[] args) {
        int len = 100;
        int testTime = 10000;
        System.out.println("功能测试开始");
        for (int i = 0; i < testTime; i++) {
            int n = (int) (Math.random() * len) + 2;
            int[][] grid = randomGrid(n);
            int startX = (int) (Math.random() * n);
            int startY = (int) (Math.random() * n);
            int targetX = (int) (Math.random() * n);
            int targetY = (int) (Math.random() * n);
            int ans1 = minDistance1(grid, startX, startY, targetX, targetY);
            int ans2 = minDistance2(grid, startX, startY, targetX, targetY);
            if (ans1 != ans2) {
                System.out.println("出错了!");
            }
        }
        System.out.println("功能测试结束");

        System.out.println("性能测试开始");
        int[][] grid = randomGrid(4000);
        int startX = 0;
        int startY = 0;
        int targetX = 3900;
        int targetY = 3900;
        long start, end;
        start = System.currentTimeMillis();
        int ans1 = minDistance1(grid, startX, startY, targetX, targetY);
        end = System.currentTimeMillis();
        System.out.println("运行dijskra算法结果: " + ans1 + ", 运行时间(毫秒) : " + (end - start));
        start = System.currentTimeMillis();
        int ans2 = minDistance2(grid, startX, startY, targetX, targetY);
        end = System.currentTimeMillis();
        System.out.println("运行A*算法结果: " + ans2 + ", 运行时间(毫秒) : " + (end - start));
        System.out.println("性能测试结束");
    }

}
```
