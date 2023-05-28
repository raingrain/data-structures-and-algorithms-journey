# 强连通分量代码模板

---

## *Java*

> - ***Tarjan算法生成强连通分量序数组、统计强连通分量个数、生成有向无环缩点图***

```java
class Solution {

    private static class StronglyConnectedComponents {

        // 邻接表，0位置弃而不用
        private final ArrayList<ArrayList<Integer>> nextMap;
        // 节点个数
        private int n;
        // 栈
        private final int[] stack;
        // 栈顶指针
        private int stackSize;
        // 遍历时统计dfn序
        private int cnt;
        // 存储每一个节点对应的dfn序
        private final int[] dfn;
        // 每一个节点所能到达的dfn序最小的节点的dfn序
        private final int[] low;
        // 一个节点所在的强连通分量，从1开始
        private final int[] scc;
        // 遍历时记录强连通来到哪里了
        private int sccn;

        public StronglyConnectedComponents(ArrayList<ArrayList<Integer>> nextMap) {
            this.nextMap = nextMap;
            n = nextMap.size();
            stack = new int[n];
            stackSize = 0;
            dfn = new int[n];
            low = new int[n];
            cnt = 0;
            scc = new int[n];
            sccn = 0;
            n--;
            createScc();
        }

        // 从每一个点开始去构建强连通分量
        // 这样子做可以避免森林等特殊情况
        // 或者两个强连通分量A和B同时指向另外一个强连通分量C
        // 你必须遍历所有节点才能构建出A和B的另外一个
        private void createScc() {
            for (int i = 1; i <= n; i++) {
                // 已经构建的就不走tarjan算法
                if (dfn[i] == 0) {
                    tarjan(i);
                }
            }
        }

        private void tarjan(int cur) {
            // 初始时low就等于自己的dfn序
            // 深度优先搜索来到++cnt号
            low[cur] = dfn[cur] = ++cnt;
            // 当前节点入栈
            stack[stackSize++] = cur;
            // 对所有子节点做tarjan
            for (int next : nextMap.get(cur)) {
                if (dfn[next] == 0) {
                    // dfn序为0表示没有dfs过，去dfs
                    tarjan(next);
                }
                // 一个节点只能有如下三种状态
                // 没有dfs过
                // dfs了，但还在栈中，没有结算
                // 已经结算，知道该节点的强连通分量信息
                // 经过上一个if后当前节点只可能是后面两种状态
                if (scc[next] == 0) {
                    // scc[next] != 0 说明已经结算，不属于当前强连通分量
                    // scc[next] == 0 说明未结算，用我所有子节点的low更新我的low，谁小取谁
                    low[cur] = Math.min(low[cur], low[next]);
                }
            }
            // 当前dfs序等于low，说明当前节点是强连通分量的入口
            if (low[cur] == dfn[cur]) {
                // 来到一个新的强连通分量，结算
                sccn++;
                // 在栈中寻找cur
                int findCur = 0;
                // 栈内元素依次出栈，直到cur也被弹出
                // 栈内包括cur，cur以上的所有元素都属于同一个强连通分量
                do {
                    // 弹出
                    findCur = stack[--stackSize];
                    // 记录强连通分量信息
                    scc[findCur] = sccn;
                } while (findCur != cur);
            }
        }

        // 强连通分量数组
        public int[] getScc() {
            return scc;
        }

        // 强连通分量个数
        public int getSccn() {
            return sccn;
        }

        // 每一个强连通分量可以看作一个点
        // 原来的图可以浓缩成用强连通分量表示的图，强连通分量之间也用节点连接
        public ArrayList<ArrayList<Integer>> getDirectedAcyclicReducedPointGraph() {
            ArrayList<ArrayList<Integer>> dag = new ArrayList<ArrayList<Integer>>();
            for (int i = 0; i <= sccn; i++) {
                dag.add(new ArrayList<Integer>());
            }
            for (int cur = 1; cur <= n; cur++) {
                // 避免一个强连通分量A中的节点cur有多个子节点在强连通分量B中导致A有多条指向B的指针
                HashSet<Integer> isVisited = new HashSet<Integer>();
                for (int next : nextMap.get(cur)) {
                    // 不是同一个强连通分量且该强连通分量没有连接过
                    if (scc[cur] != scc[next] && !isVisited.contains(scc[next])) {
                        dag.get(scc[cur]).add(scc[next]);
                        isVisited.add(scc[next]);
                    }
                }
            }
            return dag;
        }

    }

}
```

---

> ***last change: 2023/5/11***

---
