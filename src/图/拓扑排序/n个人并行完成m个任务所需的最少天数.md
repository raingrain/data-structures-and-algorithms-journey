# n个人并行完成m个任务所需的最少天数

## hulu

> - ***Question***
>   - 有 `n` 个人， `m` 个任务，任务之间有依赖记录在 `int[][] depends` 里。
>   - `depends[i] = [a, b]` 表示 `a` 任务依赖 `b` 任务的完成。
>   - `1` 个人 `1` 天可以完成 `1` 个任务，每个人都会选当前能做任务里，标号最小的任务。
>   - 一个任务所依赖的任务都完成了，该任务才能开始做。
>   - 返回 `n` 个人做完 `m` 个任务，需要几天。

---

## *Java*

> - ***小根堆 + 拓扑排序***

```java
class Solution {
    
    public static int days(int n, int m, int[][] depends) {
        if (n < 1) {
            return -1;
        }
        if (m <= 0) {
            return 0;
        }
        // getNexts[0] = {1,4}
        int[][] nexts = getNexts(depends, m);
        int[] inDegreeMap = getInDegreeMap(nexts, m);
        // 工人队列！
        PriorityQueue<Integer> workers = new PriorityQueue<>();
        for (int i = 0; i < n; i++) {
            // 这个工人几号时间点空闲
            workers.add(0);
        }
        // zeroIn ： 放着工作，放着可以开始做的工作，不能做的任务，不在其中
        // 小根堆：标号小的任务，一定要先做！
        PriorityQueue<Integer> zeroIn = new PriorityQueue<>();
        for (int i = 0; i < m; i++) {
            if (inDegreeMap[i] == 0) {
                zeroIn.add(i);
            }
        }
        // start[i] ：i之前必须完成的任务，占了几天，导致i任务只能从那天开始！
        int[] start = new int[m];
        // 完成所有任务的最大天数
        int finishAll = 0;
        int done = 0;
        while (!zeroIn.isEmpty()) { // 有任务可做
            // 当前可以做的任务中，标号最小的任务
            int job = zeroIn.poll();
            // 当前可用的工人里，最早醒的！
            int wake = workers.poll();
            // job 何时完成呢？
            // (工人醒来，开工时间)最晚的！+1
            int finish = Math.max(start[job], wake) + 1;
            finishAll = Math.max(finishAll, finish);
            done++;
            // 消除影响
            for (int next : nexts[job]) {
                // 下一级任务的最早开始时间等于它依赖的最晚结束时间
                start[next] = Math.max(start[next], finish);
                if (--inDegreeMap[next] == 0) {
                    zeroIn.add(next);
                }
            }
            workers.add(finish);
        }
        // 实际做的任务数量和给的不同，说明有环，做不了
        return done == m ? finishAll : -1;
    }
    
    public static int[][] getNexts(int[][] depends, int m) {
        Arrays.sort(depends, Comparator.comparingInt(a -> a[1]));
        int n = depends.length;
        int[][] nexts = new int[m][0];
        if (n == 0) {
            return nexts;
        }
        int size = 1;
        for (int i = 1; i < n; i++) {
            if (depends[i - 1][1] != depends[i][1]) {
                int from = depends[i - 1][1];
                nexts[from] = new int[size];
                for (int k = 0, j = i - size; k < size; k++, j++) {
                    nexts[from][k] = depends[j][0];
                }
                size = 1;
            } else {
                size++;
            }
        }
        int from = depends[n - 1][1];
        nexts[from] = new int[size];
        for (int k = 0, j = n - size; k < size; k++, j++) {
            nexts[from][k] = depends[j][0];
        }
        return nexts;
    }
    
    public static int[] getInDegreeMap(int[][] nexts, int m) {
        int[] inDegreeMap = new int[m];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < nexts[i].length; j++) {
                inDegreeMap[nexts[i][j]]++;
            }
        }
        return inDegreeMap;
    }
    
    public static void main(String[] args) {
        // 2 -> 5 -> 6
        //           |
        //           v
        // 1 -> 4 -> 7
        //      ^
        //      |
        // 0 -> 3
        // 两个人
        // {1，2} 工人队列
        // 0 : 干0号工作 ，1
        // 0 : 干1号工作 ，1
        // 1 : 干2号工作，2
        int[][] d = {
                {3, 0},
                {4, 1},
                {5, 2},
                {4, 3},
                {6, 5},
                {7, 4},
                {7, 6}
        };
        System.out.println(days(3, 8, d));
        System.out.println(days(2, 8, d));
    }
    
}
```
