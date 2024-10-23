# 两个有序数组间相加和的Topk问题

## [两个有序数组间相加和的Topk问题](https://www.nowcoder.com/questionTerminal/7201cacf73e7495aa5f88b223bbbf6d1)

> - **Question**
>   - 给定两个有序数组 `arr1` 和 `arr2` ，再给定一个整数 `k` ，返回来自 `arr1` 和 `arr2` 的两个数相加和最大的前 `k` 个，两个数必须分别来自两个数组，按照降序输出。
>   - **输入描述**
>     - 第一行三个整数， `N, K` 分别表示数组 `arr1, arr2` 的大小，以及需要询问的数
>     - 接下来一行 `N` 个整数，表示 `arr1` 内的元素
>     - 再接下来一行 `N` 个整数，表示 `arr2` 内的元素
>   - **输出描述**
>     - 输出 `K` 个整数表示答案
>   - **Tips**
>     - `1 <= N <= 10^5`
>     - `0 <= arr1, arr2 <= 10^9`
>     - `1 <= K <= 2N`

## Java

> - **大根堆**

```java
public class Main {
    
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            int N = (int) in.nval;
            in.nextToken();
            int K = (int) in.nval;
            int[] arr1 = new int[N];
            int[] arr2 = new int[N];
            for (int i = 0; i < N; i++) {
                in.nextToken();
                arr1[i] = (int) in.nval;
            }
            for (int i = 0; i < N; i++) {
                in.nextToken();
                arr2[i] = (int) in.nval;
            }
            int[] topK = topKSum(arr1, arr2, K);
            for (int i = 0; i < K; i++) {
                out.print(topK[i] + " ");
            }
            out.println();
            out.flush();
        }
    }
    
    // 放入大根堆中的结构
    public static class Node {
        
        // arr1中的位置
        public int index1;
        // arr2中的位置
        public int index2;
        // arr1[index1] + arr2[index2]的值
        public int sum;
        
        public Node(int index1, int index2, int sum) {
            this.index1 = index1;
            this.index2 = index2;
            this.sum = sum;
        }
        
    }
    
    public static int[] topKSum(int[] arr1, int[] arr2, int topK) {
        // 越界位置
        if (arr1 == null || arr2 == null || topK < 1) {
            return null;
        }
        // 有可能k超过了两个数组的所有组合
        topK = Math.min(topK, arr1.length * arr2.length);
        // 结果数组
        int[] ans = new int[topK];
        // 填结果数组的索引
        int ansIndex = 0;
        // 大根堆
        PriorityQueue<Node> maxHeap = new PriorityQueue<>((o1, o2) -> o2.sum - o1.sum);
        // 哈希集合用于判断一个位置有没有加过大根堆
        HashSet<String> set = new HashSet<>();
        // 因为两个数组都有序，我们倒序遍历，保证获得的都是较大的
        int arr1Index = arr1.length - 1;
        int arr2Index = arr2.length - 1;
        // 两边数组的最后一个数字都是最大的，加起来自然是top1
        maxHeap.add(new Node(arr1Index, arr2Index, arr1[arr1Index] + arr2[arr2Index]));
        // 索引用连接符连接起来表示加过了
        set.add(arr1Index + "_" + arr2Index);
        // 结果数组还没有填满
        while (ansIndex != topK) {
            // 大根堆堆顶，当前最大的数字
            Node curNode = maxHeap.poll();
            assert curNode != null;
            // 填答案
            ans[ansIndex++] = curNode.sum;
            // 组成当前答案的两个位置
            arr1Index = curNode.index1;
            arr2Index = curNode.index2;
            // 哈希集合中移除
            // 你都已经弹出堆了，说明你是之前的循环中加入堆的，那么你的左和上都加过了，没人碰得到你会把你再加进去
            set.remove(arr1Index + "_" + arr2Index);
            // 想象一张dp表
            // 行是arr1的每一个位置
            // 列是arr2的每一个位置
            // 从右下角开始往左上填位置，填当前来到位置的左和上
            // 没越界且没加过
            if (arr1Index - 1 >= 0 && !set.contains((arr1Index - 1) + "_" + arr2Index)) {
                // 入集合
                set.add((arr1Index - 1) + "_" + arr2Index);
                // 入堆
                maxHeap.add(new Node(arr1Index - 1, arr2Index, arr1[arr1Index - 1] + arr2[arr2Index]));
            }
            if (arr2Index - 1 >= 0 && !set.contains(arr1Index + "_" + (arr2Index - 1))) {
                set.add(arr1Index + "_" + (arr2Index - 1));
                maxHeap.add(new Node(arr1Index, arr2Index - 1, arr1[arr1Index] + arr2[arr2Index - 1]));
            }
        }
        return ans;
    }
    
}
```
