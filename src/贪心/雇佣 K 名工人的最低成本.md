# 雇佣 K 名工人的最低成本

## [857. 雇佣 K 名工人的最低成本](https://leetcode.cn/problems/minimum-cost-to-hire-k-workers/)

> - ***Question***
>   - 有 `n` 名工人。 给定两个数组 `quality` 和 `wage` ，其中 `quality[i]` 表示第 `i` 名工人的工作质量，其最低期望工资为 `wage[i]` 。
>   - 现在我们想雇佣 `k` 名工人组成一个工资组。在雇佣一组 `k` 名工人时，我们必须按照下述规则向他们支付工资：
>     - 对工资组中的每名工人，应当按其工作质量与同组其他工人的工作质量的比例来支付工资。
>     - 工资组中的每名工人至少应当得到他们的最低期望工资。
>   - 给定整数 `k` ，返回组成满足上述条件的付费群体所需的最小金额。在实际答案的 `10^-5` 以内的答案将被接受。
>   - ***tips:***
>     - `n == quality.length == wage.length`
>     - `1 <= k <= n <= 10^4`
>     - `1 <= quality[i], wage[i] <= 10^4`

---

## *Java*

> - ***贪心 + 小根堆***
>   - 垃圾指数是一个工人的薪资除以工作质量。
>   - 那么首先假设我们已经选择了某一个工资组，组成成员为 `[h1, ..., hk]` ，其中 `hi` 表示第 `i` 个工人，整个工作组的总工作质量为 `totalq` ，总的支付金额为 `totalc` 。
>   - 贪心策略为当我们以某一个工人 `x` 作为一个工资组中垃圾指数最高时，工资组中其他人员只需要在垃圾指数小于工人 `x` 的集合中选择工作质量最少的 `k- 1` 名工人来组成工资组即可，此时便能达到以工人 `x` 为垃圾指数最高的工资组的总工作量最小，从而达到以工人 `x` 为垃圾指数最高的工资组的最小工资开销。
>   - 然后我们枚举以每一个能成为工资组中垃圾指数最大的工人来计算最小工资组开销，然后取其中的最小即可。在处理的过程中，我们可以先将工人按照垃圾指数进行升序排序，然后在遍历过程中可以用优先队列来维护之前工作质量最少的 `k - 1` 名工人。

```java
class Solution {

    public static class Employee {

        public double rubbishDegree;
        public int quality;

        public Employee(int wage, int quality) {
            rubbishDegree = (double) wage / (double) quality;
            this.quality = quality;
        }

    }

    public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        Employee[] employees = new Employee[quality.length];
        for (int i = 0; i < quality.length; i++) {
            employees[i] = new Employee(wage[i], quality[i]);
        }
        Arrays.sort(employees, (a, b) -> a.rubbishDegree <= b.rubbishDegree ? -1 : 1);
        PriorityQueue<Integer> minHeap = new PriorityQueue<>((a, b) -> b - a);
        double ans = Double.MAX_VALUE;
        for (int i = 0, qualitySum = 0; i < n; i++) {
            int curQuality = employees[i].quality;
            if (minHeap.size() < k) {
                qualitySum += curQuality;
                minHeap.add(curQuality);
                if (minHeap.size() == k) {
                    ans = Math.min(ans, qualitySum * employees[i].rubbishDegree);
                }
            } else {
                if (minHeap.peek() > curQuality) {
                    qualitySum += curQuality - minHeap.poll();
                    minHeap.add(curQuality);
                }
                ans = Math.min(ans, qualitySum * employees[i].rubbishDegree);
            }
        }
        return ans;
    }

}

// 新版
class Solution {

    public static class Employee {

        public double ratio; // 薪水 / 质量的比例
        public int quality;

        public Employee(double r, int q) {
            ratio = r;
            quality = q;
        }

    }

    public static double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int n = quality.length;
        Employee[] employees = new Employee[n];
        for (int i = 0; i < n; i++) {
            employees[i] = new Employee((double) wage[i] / quality[i], quality[i]);
        }
        // 根据比例排序，比例小的在前，比例大的在后
        Arrays.sort(employees, (a, b) -> a.ratio <= b.ratio ? -1 : 1);
        // 大根堆，用来收集最小的前k个质量数值
        PriorityQueue<Integer> heap = new PriorityQueue<Integer>((a, b) -> b - a);
        // 堆里，最小的前k个质量数值，总和是多少
        int qualitySum = 0;
        double ans = Double.MAX_VALUE;
        for (int i = 0, curQuality; i < n; i++) {
            curQuality = employees[i].quality;
            if (heap.size() < k) { // 堆没满
                qualitySum += curQuality;
                heap.add(curQuality);
                if (heap.size() == k) {
                    ans = Math.min(ans, qualitySum * employees[i].ratio);
                }
            } else { // 堆满了
                if (heap.peek() > curQuality) {
                    qualitySum += curQuality - heap.poll();
                    heap.add(curQuality);
                    ans = Math.min(ans, qualitySum * employees[i].ratio);
                }
            }
        }
        return ans;
    }

}
```
