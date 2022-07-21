### 线段树代码模板Java：

```java
class Solution {

    public static class SegmentTree {

        // 将原始数组向后推移一位，以适应线段树是从1开始的，0位置置空
        private int[] arr;
        // 对应区间的累加和
        private int[] sum;
        // add方法的懒加载信息
        // lazy[i]代表对应区间上每个数字要加的数
        private int[] lazy;
        // 要change[i]指要change成的个数
        private int[] change;
        // 避免change[i]等于0的歧义，true表示要更新，false反之
        private boolean[] update;

        public SegmentTree(int[] origin) {
            int maxLen = origin.length + 1;
            arr = new int[maxLen];
            for (int i = 1; i < arr.length; i++) {
                arr[i] = origin[i - 1];
            }
            sum = new int[maxLen * 2];
            lazy = new int[maxLen * 2];
            change = new int[maxLen * 2];
            update = new boolean[maxLen * 2];
        }

        //
        public void build(int left, int right, int head) {
            if (left == right) {
                sum[head] = arr[head];
                return;
            } else {
                int mid = (left + right) >> 1;
                build(left, mid, head * 2);
                build(mid + 1, right, head * 2 + 1);
            }
        }

        // 计算head位置对应的区间的累加和
        private void pushUp(int head) {
            sum[head] = sum[head * 2] + sum[head * 2 + 1];
        }

        private void pushDown(int head, int numOfLeftTree, int numOfRightTree) {
            // 是要更新的
            if (update[head]) {
                // 把确定更新的信息推送到下一层
                update[head * 2] = true;
                update[head * 2 + 1] = true;
                // 把要更新成的数字推送到下一层
                change[head * 2] = change[head];
                change[head * 2 + 1] = change[head];
                // 因为需要更新，所以要add的数据直接被清空
                lazy[head * 2] = 0;
                lazy[head * 2 + 1] = 0;
                // 因为对应范围内全部都更新了，那么累加和自然就变成更新后的值乘以范围
                sum[head * 2] = change[head] * numOfLeftTree;
                sum[head * 2 + 1] = change[head] * numOfRightTree;
            }
            // 需要对add数据进行懒更新
            // 刚更新完是不会有add数据存在的，在更新时都被清理了
            if (lazy[head] != 0) {
                // 懒加载信息被推送而来
                lazy[head * 2] += lazy[head];
                lazy[head * 2 + 1] += lazy[head];
                // 对应的区间和增加
                sum[head * 2] = lazy[head] * numOfLeftTree;
                sum[head * 2 + 1] = lazy[head] * numOfRightTree;
                // 懒加载信息已经推送到下一层，本层懒加载信息归0
                lazy[head] = 0;
            }
        }

        // 数组中的L-R位置全部加num
        public void add(int L, int R, int num, int left, int right, int head) {
            // 任务区间能把当前区间囊括住
            // 直接在这一层进行操作并更新懒加载信息
            if (L <= left || R >= right) {
                // 如果新老两个任务都能把当前区间囊括，就一起停在这一层
                sum[head] += num * (right - left + 1);
                lazy[head] += num;
            } else {
                // 任务没有把当前区间全部囊括
                int mid = (left + right) >> 1;
                pushDown(head, mid - left + 1, right - mid);
                if (L <= mid) {
                    add(L, R, num, left, mid, head * 2);
                }
                if (R >= mid) {
                    add(L, R, num, mid + 1, right, head * 2 + 1);
                }
                pushUp(head);
            }
        }

        public void update(int L, int R, int num, int left, int right, int head) {
            // 任务区间能把当前区间囊括住
            // 当前区间更新，求累加和，清除懒加载信息一条龙服务
            if (L <= left || R >= right) {
                update[head] = true;
                change[head] = num;
                sum[head] = num * (right - left + 1);
                lazy[head] = 0;
            } else {
                // 任务没有把当前区间全部囊括
                int mid = (left + right) >> 1;
                // 新来的任务不能被head层拦截
                // 先把head层存下来的任务发到下一层
                // 然后再把新来的任务下发（因为新来的也不能被拦截）
                pushDown(head, mid - left + 1, right - mid);
                if (L <= mid) {
                    update(L, R, num, left, mid, head * 2);
                }
                if (R > mid) {
                    update(L, R, num, mid + 1, right, head * 2 + 1);
                }
                pushUp(head);
            }
        }

        public long query(int L, int R, int left, int right, int head) {
            // 任务区间能把当前区间囊括住
            // 直接查询累加和并返回
            if (L <= left && right <= R) {
                return sum[head];
            }
            int mid = (left + right) >> 1;
            pushDown(head, mid - left + 1, right - mid);
            long ans = 0;
            if (L <= mid) {
                ans += query(L, R, left, mid, head * 2);
            }
            if (R > mid) {
                ans += query(L, R, mid + 1, right, head * 2 + 1);
            }
            return ans;
        }
    }
}
```
