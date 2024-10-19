# IPO前能够获取的最大总资本

## [502. IPO](https://leetcode.cn/problems/ipo/)

> - ***Question***
>   - 假设力扣即将开始 `IPO` ，为了以更高的价格将股票卖给风险投资公司，力扣希望在 `IPO` 之前开展一些项目以增加其资本，由于资源有限，它只能在 `IPO` 之前完成最多 `k` 个不同的项目，帮助力扣设计完成最多 `k` 个不同项目后得到最大总资本的方式。
>   - 给你 `n` 个项目，对于每个项目 `i` ，它都有一个纯利润 `profits[i]` ，和启动该项目需要的最小资本 `capital[i]` 。
>   - 最初你的资本为 `w` ，当完成一个项目时你将获得纯利润，且利润将被添加到你的总资本中。
>   - 总而言之，从给定项目中选择最多 `k` 个不同项目的列表以最大化最终资本，并输出最终可获得的最多资本。
>   - 答案保证在 `32` 位有符号整数范围内。
>   - ***tips:***
>     - `1 <= k <= 10^5`
>     - `0 <= w <= 10^9`
>     - `n == profits.length`
>     - `n == capital.length`
>     - `1 <= n <= 10^5`
>     - `0 <= profits[i] <= 10^4`
>     - `0 <= capital[i] <= 10^9`

---

## *Java*

> - ***贪心 + 大小堆***
>   - 想象一下 `RPG` 中的打怪，你在初期打怪只能选你打的过的，但奖励最为丰厚的，你获得了奖励，你的实力提升了，你就可以选取实力更高的怪中奖励最多的来打，周而复始，等级提升。
>   - 本题采用贪心+大小堆来解决，先构建项目类，存储每个项目的净利润和花销信息，然后把所有项目按照花销存储在小根堆中，大根堆用来加入项目，其按照利润排序。
>   - 进行一个 `k` 次的 `for` 循环，每一次都先把小根堆中花销小于当前资本的项目弹出到大根堆中，然后弹出（做）大根堆中利润最多的项目，资本加上这个利润，如果小根堆没有弹出且大根堆为空，那么意味着剩余的项目都做不了，直接返回。

```java
class Solution {
    
    private static class Program {
        
        public int profit;
        public int capital;
        
        public Program(int profit, int capital) {
            this.profit = profit;
            this.capital = capital;
        }
        
    }
    
    private int findMaximizedCapitalByGreedy(int k, int w, int[] profits, int[] capital) {
        // 按照花费排序的小根堆
        PriorityQueue<Program> minCapitalQueue = new PriorityQueue<>(new Comparator<Program>() {
            @Override
            public int compare(Program o1, Program o2) {
                return o1.capital - o2.capital;
            }
        });
        // 按照利润排序的大根堆
        PriorityQueue<Program> maxProfitQueue = new PriorityQueue<>(new Comparator<Program>() {
            @Override
            public int compare(Program o1, Program o2) {
                return o2.profit - o1.profit;
            }
        });
        // 把所有项目加入到小根堆里面
        for (int i = 0; i < profits.length; i++) {
            minCapitalQueue.add(new Program(profits[i], capital[i]));
        }
        // 最多做k次
        for (int i = 0; i < k; i++) {
            // 每次都把小根堆中能用手里的资本做的项目加入到大根堆中
            while (!minCapitalQueue.isEmpty() && minCapitalQueue.peek().capital <= w) {
                maxProfitQueue.add(minCapitalQueue.poll());
            }
            // 如果小根堆里面没有能做的，大根堆也没有，那么剩下的项目必然没有本金完成，直接返回
            if (maxProfitQueue.isEmpty()) {
                return w;
            } else {
                // 做目前能做的利润最大的项目
                w += maxProfitQueue.poll().profit;
            }
        }
        return w;
    }
    
    public int findMaximizedCapital(int k, int w, int[] profits, int[] capital) {
        return findMaximizedCapitalByGreedy(k, w, profits, capital);
    }
    
}
```
