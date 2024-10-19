# 蓄水池算法

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第9章 其他题目 蓄水池算法

> - ***Question***
>   - 有一台机器按自然数序列的方式吐出球：1号球，2号球，3号球……
>   - 你有一个袋子，袋子最多只能装下 `K` 个球，并且除袋子以外，你没有更多的空间。
>   - 设计一种选择方式，使得当机器吐出第 `N` 号球的时候（ `N` 大于 `K` ），袋子中的球数是 `K` 个，同时可以保证从 `1` 号球到 `N` 号球中的每一个被选进袋子的概率都是 `K/N` 。
>   - 举一个更具体的例子，有一个只能装下 `10` 个球的袋子，当吐出 `100` 个球时，袋子里有 `10` 个球，并且 `1~100` 号中的每一个球被选中的概率都是 `10/100` 。然后继续吐球，当吐出 `1000` 个球时，袋子里有 `10` 个球，并且 `1~1000` 号中的每一个球被选中的概率都是 `10/1000` 。继续吐球，当吐出 `i` 个球时，袋子里有 `10` 个球，并且 `1~i` 号中的每一个球被选中的概率都是 `10/i` ，即吐球的同时，已经吐出的球被选中的概率也动态地变化。

---

## *Java*

> - ***蓄水池算法***
>   - 对于第 `1` 到第 `k` 号球，直接放进袋子中；
>   - 对于第 `i (i > k)` 号球，以 `k/i` 的概率决定是否将第 `i` 号球放入袋子中：
>     - 如果决定将第 `i` 号球扔掉，那就永远扔掉
>     - 如果决定将第 `i` 号球放进袋子，请以 `1/k` 的概率随机地扔掉袋子中的一个球并用第 `i` 号球顶替它的位置
>   - 如下证明能保证从 `1` 号球到 `n` 号球中的每一个被选进袋子的概率都是 `k/n` ：
>     ![image](球号小于等于k时的证明.png)
>     ![image](球号大于k时的证明.png)
> - ***应用场景***
>   - 某游戏全球各个地区都有一台服务器，并有一台总机。
>   - 假设有这样一个活动：在 `1` 号 `00:00:00 - 23:59:59` 之间登录的用户中抽取 `100` 个用户给予奖励，怎么实现最好？
>   - 直接在总机采用蓄水池算法，准备一个大小为 `100` 的空间来实时更新“抽取中”的用户，“球”即为各个服务器实时提交的当日首次登录的用户，到 `2` 号时直接公布“袋子”中的用户作为获奖者即可。

```java
class Solution {

    // 返回1-i的随机整数
    public static int rand(int i) {
        // [0, i) -> [1, i + 1) -> [1, i]
        return (int) ((Math.random() * i) + 1);
    }

    // k是袋子长度
    // i是机器中球的个数
    public static int[] getKNumsRand(int k, int i) {
        // 没袋子或者没球
        if (i < 1 || k < 1) {
            return null;
        } else {
            // 实际的袋子
            int[] bag = new int[Math.min(k, i)];
            // 前k个数直接进袋子
            for (int bagi = 0; bagi < bag.length; bagi++) {
                bag[bagi] = bagi + 1;
            }
            for (int index = k + 1; index <= i; index++) {
                // 第i个数以k/i的概率进袋子 -> 在1-i之间随机抽数，抽到小于等于k的概率为k/i
                if (rand(index) <= k) {
                    // i位置的数随机替换袋子中的某个数
                    bag[rand(k) - 1] = index;
                }
            }
            return bag;
        }
    }
}

// 对数器验证
public class Main {

    public static class Pool {

        private int size;

        public int[] bag;

        public Pool(int s) {
            size = s;
            bag = new int[s];
        }

        // 是否要i号球
        // size/i的几率决定要
        // 剩下的几率决定不要
        private boolean pick(int i) {
            return (int) (Math.random() * i) < size;
        }

        // 袋子里0...size-1个位置
        // 哪个空间的球扔掉，让i号球进来
        private int where() {
            return (int) (Math.random() * size);
        }

        public void enter(int i) {
            if (i <= size) {
                bag[i - 1] = i;
            } else {
                if (pick(i)) {
                    bag[where()] = i;
                }
            }
        }

        public int[] getBag() {
            return bag;
        }

    }

    public static void main(String[] args) {
        System.out.println("测试开始");
        int n = 41; // 一共吐出多少球
        int m = 10; // 袋子大小多少
        int testTimes = 10000; // 进行多少次实验
        int[] cnt = new int[n + 1];
        for (int k = 0; k < testTimes; k++) {
            Pool pool = new Pool(m);
            for (int i = 1; i <= n; i++) {
                pool.enter(i);
            }
            int[] bag = pool.getBag();
            for (int num : bag) {
                cnt[num]++;
            }
        }
        System.out.println("机器吐出到" + n + "号球, " + "袋子大小为" + m);
        System.out.println("每个球被选中的概率应该接近" + (double) m / n);
        System.out.println("一共测试" + testTimes + "次");
        for (int i = 1; i <= n; i++) {
            System.out.println(i + "被选中次数 : " + cnt[i] + ", 被选中概率 : " + (double) cnt[i] / testTimes);
        }
        System.out.println("测试结束");
    }

}
```
