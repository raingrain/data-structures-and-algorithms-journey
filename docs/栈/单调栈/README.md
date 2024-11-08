# 单调栈结构

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第1章 栈和队列 单调栈结构

> - **Question 1**
>   - 给定一个不含有重复值的数组 `arr` ，找到每一个 `i` 位置左边和右边离 `i` 位置最近且值比 `arr[i]` 小的位置，返回所有位置相应的信息。
>   - 返回格式为一个二维数组， `ans[i]` 是一个长度为 `2` 的一维数组， `ans[i][0]` 表示 `arr[i]` 左边比 `arr[i]` 小且离 `arr[i]` 最近的元素索引， `ans[i][1]` 代表右边的，没有小于当前元素的元素就填 `-1` 。
> - **Question 2**
>   - 如果这个数组含有重复值又该如何做？

## Java

> - **用途**
>   - 求左右最近小值索引数组
> - **不带有重复值的单调栈**
>   - 准备一个存储索引的栈，由于求的是左右两边的最小值，要求栈中索引对应的元素自栈底向栈顶由小到大排列。
>   - 依次遍历数组元素：
>     - 栈为空时直接入栈。
>     - 如果栈不为空且当前栈顶索引对应的数组元素比当前遍历到的元素小，那么当前索引直接入栈。
>     - 如果栈不为空且当前栈顶索引对应的数组元素比当前遍历到的元素大，就一直弹出栈顶，直到栈顶索引对应的数组元素比当前遍历到的元素小或者栈为空，再把当前索引入栈，弹出栈顶时往结果数组中填信息：
>       - 当前索引就是弹出的栈顶索引对应的数组元素的最右小值的索引，而弹出后的新栈顶索引就是弹出的栈顶索引对应的数组元素的最左小值的索引。
>       - 如果弹出后栈底为空，则最左小值索引为 `-1` 。
>   - 但很显然结果的添加是非线性的，在遍历完数组元素后可能栈中还有索引，依次弹出并处理信息，最右小值索引一律都是 `-1` ，最左小值索引和遍历时一样。
>   - 单调栈有一个隐性的本质，即一个元素与它的最近小值元素之间的元素一定比这个元素大，这可以用单调栈的结构来证明：如果栈中 `b` 压着 `a` （即 `a` 对应的元素比 `b` 小），那么数组中 `a` 与 `b` 之间不可能有比 `a` 小的，有的话 `a` 早就被弹出了；也没有大于 `a` 小于 `b` 的，因为如果数组有栈中没有的话一定是一个比其小的数但还大于 `a` 的数把它弹出栈了，但它也需要别人弹，循环下去这与栈中 `a` 与 `b` 之间无元素矛盾；因为没有重复值也没有等于 `b` 的；只有 `a` 与 `b` 之间都是大于 `b` 的， `b` 入栈时小于这些数把它们都弹出了，这才合理。
> - **带重复值的单调栈**
>   - 基本思路和不带重复值的相同，不过入栈的是索引链表，弹出栈顶直到相等，相等时加入到栈顶链表的末尾，处理信息时整条链表的索引的信息是一样的，最左小值索引用新栈顶的索引链表的最后一个元素（这样才能保持最近），其他细节基本一样。
>   - 这引出了另外一个本质：重复值之间具有连通性，大家的最近小值都一样。
> - 由于每一个索引都只涉及一次出栈和入栈，所以两种处理方式的时间复杂度都为 `O(N)` 。

```java
class MonotoneStack {
    
    // 无重复着数组
    public int[][] getNearLessNoRepeat(int[] arr) {
        int[][] ans = new int[arr.length][2];
        // 以索引作为元素
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < arr.length; ++i) {
            // 当前栈不为空且栈顶大于当前元素
            while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
                // 弹出栈顶
                int popIndex = stack.pop();
                // 处理它的最近小值信息
                // 如果栈为空就没有最左小值，否则就是新栈顶
                ans[popIndex][0] = stack.isEmpty() ? -1 : stack.peek();
                // 最右小值就是使其弹出的遍历到的索引
                ans[popIndex][1] = i;
            }
            // 处理完了再入栈
            stack.push(i);
        }
        // 遍历完数组后栈顶可能还有元素
        while (!stack.isEmpty()) {
            int popIndex = stack.pop();
            ans[popIndex][0] = stack.isEmpty() ? -1 : stack.peek();
            // 没有使其弹出的元素，没有最右小值
            ans[popIndex][1] = -1;
        }
        return ans;
    }
    
    // 有重复值数组
    public int[][] getNearLessRepeat(int[] arr) {
        int[][] ans = new int[arr.length][2];
        // 栈元素为索引链表，链表内按照遍历顺序存储值相同的索引
        Deque<List<Integer>> stack = new ArrayDeque<>();
        for (int i = 0; i < arr.length; ++i) {
            while (!stack.isEmpty() && arr[stack.peek().get(0)] > arr[i]) {
                 // 如果栈不为空且当前索引对应值小于栈顶索引对应值，对栈顶链表内元素执行结算操作
                // 处理整个节点链表
                List<Integer> popIndex = stack.pop();
                // 由于连通性，相同元素的最近小值相同
                // 最左小值是新栈顶的末尾，即多个相同小值的最右索引
                int leftNearLessIndex = stack.isEmpty() ? -1 : stack.peek().get(stack.peek().size() - 1);
                for (Integer index : popIndex) {
                    // 链表内所有索引的结果相同
                    ans[index][0] = leftNearLessIndex;
                    ans[index][1] = i;
                }
            }
            if (!stack.isEmpty() && arr[stack.peek().get(0)] == arr[i]) {
                 // 如果当前索引对应值等于栈顶索引对应值，直接就加到栈顶链表末尾
                stack.peek().add(i);
            } else {
                // 如果当前栈为空或者当前索引对应值大于栈顶索引对应值，直接建一个新链表入栈
                ArrayList<Integer> list = new ArrayList<>();
                list.add(i);
                stack.push(list);
            }
        }
        // 处理栈中剩余元素
        while (!stack.isEmpty()) {
            List<Integer> popIndex = stack.pop();
            int leftNearLessIndex = stack.isEmpty() ? -1 : stack.peek().get(stack.peek().size() - 1);
            for (Integer index : popIndex) {
                ans[index][0] = leftNearLessIndex;
                ans[index][1] = -1;
            }
        }
        return ans;
    }
    
}
```
