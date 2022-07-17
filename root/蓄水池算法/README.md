# [蓄水池算法]()

### 题意

![image](https://user-images.githubusercontent.com/75558694/179391543-8734098e-f9ab-447b-8270-845fd65395a4.png)

---

### Java实现

#### *思路*

> - **蓄水池算法**
>   ---
>   1. 对于第1到第k号球，直接放进袋子中；
>   2. 对于第i号球（i大于k），以k/i的概率决定是否将第i号球放入袋子中：
>      - 如果决定将第i号球扔掉，那就永远扔掉
>      - 如果决定将第i号球放进袋子，请以1/k的概率随机地扔掉袋子中的一个球并用第i号球顶替它的位置
>   3. 证明这个过程能保证从1号球到n号球中的每一个被选进袋子的概率都是k/n：
>      ![image](https://user-images.githubusercontent.com/75558694/179391887-5779cde3-d8be-4711-937f-0dfb2fe92b35.png)

#### *代码*

```java
class Solution {

    // 应用场景：
    // 某游戏全球各个地区都有一台服务器，并有一台总机。
    // 假设有这样一个活动：在1号00:00:00 - 23:59:59之间登录的用户中抽取100个用户给予奖励，怎么实现最好？
    // 直接在总机采用蓄水池算法，准备一个大小为100的空间来实时更新“抽取中”的用户，“球”即为各个服务器实时提交的当日首次登录的用户，到2号时直接公布“袋子”中的用户作为获奖者即可。

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
```
