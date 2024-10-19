# AC自动机代码模板

## [P5357 【模板】AC 自动机](https://www.luogu.com.cn/problem/P5357)

> - ***Question***
>   - 给你一个文本串 `S` 和 `n` 个模式串 `T1~n` ，请你分别求出每个模式串 `Ti` 在 `S` 中出现的次数。
>   - ***输入描述***
>     - 第一行包含一个正整数 `n` 表示模式串的个数。
>     - 接下来 `n` 行，第 `i` 行包含一个由小写英文字母构成的非空字符串 `Ti` 。
>     - 最后一行包含一个由小写英文字母构成的非空字符串 `S` 。
>     - 数据不保证任意两个模式串不相同。
>   - ***输出描述***
>     - 输出包含 `n` 行，其中第 `i` 行包含一个非负整数表示 `Ti` 在 `S` 中出现的次数。
>   - ***tips:***
>     - `1 <= n <= 2 * 10^5`
>     - `len(S) <= 2 * 10^6`
>     - `sum(len(Ti)) <= 2 * 10^5`

---

## *Java*

> - ***AC自动机***
>   - 一种基于前缀树和 `KMP` 算法改进而来的用于在大文本中快速查找敏感词的数据结构。
>   - 关键的改进在于构建完前缀树后引入的 `fail` 指针以及 `fail` 指针的连接和基于 `fail` 指针的查找规则。
>   - 基于 `fail` 指针的跳转策略继承了 `KMP` 算法中“忽略”的思想，即我一定知道你不是我要匹配的字符串的起点（前缀到后缀起点之间的字符），我直接忽略你。（在前缀树上像猴子一样摆荡， `fail` 指针就像被抓住的枝条）
>   - 并且 `fail` 保证了忽略的严格性，即为了不错过任何一个字符尽可能少的忽略掉字符。
>   - 具体过程和规则看代码，不懂就看视频回忆。

```java
class ACNode {
    
    // 当前节点之前的路径有一个结束了的敏感词
    public String end;
    // 当前节点之前的路径代表的敏感词在文本中是否被找到过
    public boolean endUse;
    // AC自动机就是加上了fail指针的前缀树
    // 前缀树最大的改动意义何在？意义在于保留所有匹配成功的可能性！思想来自KMP算法，一旦匹配失败，就通过fail指针绕圈找到能继续的节点，然后继续匹配
    // AC自动机上的某个节点a，表示某个目标串的前缀串s的终止节点
    // 所有目标字符串的前缀串，要求和s的某个后缀串完全一样，并且要求长度最大，并且不能是s的整体
    // 满足这三点的前缀串假设为t，那么节点a的fail指针就指向t的终止节点
    // 使用经典AC自动机，会出现fail指针绕圈的现象，具体有如下三个场景:
    // 1. 建立AC自动机时设置fail指针；遍历文章时，匹配失败去寻找支路。都需要fail指针绕圈
    // 2. 遍历文章时，不知道是否命中了某个目标字符串，需要fail指针绕圈，进行词频传递。优化方式：只让当前节点收集词频，在遍历文章结束之后，再统一进行如下处理，防止绕圈：根据fail指针建立反图，然后利用图的遍历来汇总每个节点的词频
    // 3. 遍历文章时，不知道是否命中了某个目标字符串，需要fail指针绕圈，进行及时报警。优化方式：在设置fail指针时，把命中标记前移防止绕圈。
    // 经过这样的优化，在遍历文章时，就不需要fail指针的跳转了，甚至可以忽略fail指针的存在，操作fail指针只需要发生在建立AC自动机时，或者文章遍历结束后的离线处理
    public ACNode fail;
    // 我有几条子路径
    // 这里用26个小写英文字母来表示文本
    // 如果不限制的话可以使用HashSMap
    public ACNode[] nexts;
    
    // 初始都是空或false
    public ACNode() {
        end = null;
        endUse = false;
        fail = null;
        nexts = new ACNode[26];
    }
    
}

class ACAutomaton {
    
    // 前缀树的根节点
    public ACNode root;
    
    
    // 建立好根节点
    public ACAutomaton() {
        root = new ACNode();
    }
    
    // 往前缀树中插入敏感词s
    public void insert(String s) {
        char[] str = s.toCharArray();
        // 在前缀树中遍历
        ACNode cur = root;
        for (char c : str) {
            // 出现的index都是用来换算小写字母在长度为26的数组中的位置
            int index = c - 'a';
            // 如果我的子路径中没有你我就建一个
            if (cur.nexts[index] == null) {
                cur.nexts[index] = new ACNode();
            }
            // 跳转到子路径上
            cur = cur.nexts[index];
        }
        // 把到的最后一个节点的end进行标记，这是一个敏感词的结尾
        cur.end = s;
    }
    
    // 把所有节点的fail指针挂好
    // 默认根节点的fail指针指向空
    // 默认第一层节点的fail指针指向root
    public void build() {
        // 节点的挂载顺序为宽度优先遍历
        // 用队列来实现宽度优先遍历
        Queue<ACNode> queue = new ArrayDeque<>();
        queue.add(root);
        ACNode cur = root;
        ACNode curFail = null;
        while (!queue.isEmpty()) {
            // 我们在一个节点弹出时将其子节点的fail指针挂载好
            // 因为如果弹出时挂自己的就没法找到自己的父节点
            cur = queue.poll();
            for (int i = 0; i < 26; ++i) {
                if (cur.nexts[i] != null) {
                    // 先默认fail指针指向root，如果能改再改，不然肯定指向root
                    cur.nexts[i].fail = root;
                    // 通过fail链找cur.nexts[i]能挂载的
                    curFail = cur.fail;
                    // 一个节点的fail指针确定规则：
                    // 如果我父亲的fail指针链上的节点，只要找到一个节点其子路径也有next[i]（即有相同字符），那我的fail救指向这个相同字符
                    // 如果遍历到空还没有，那我就挂载到默认的root上
                    while (curFail != null) {
                        if (curFail.nexts[i] != null) {
                            cur.nexts[i].fail = curFail.nexts[i];
                            break;
                        }
                        curFail = curFail.fail;
                    }
                    // 挂载好后再把子节点加进队列
                    queue.add(cur.nexts[i]);
                }
            }
        }
    }
    
    public List<String> containWords(String content) {
        char[] str = content.toCharArray();
        // 存储答案
        List<String> ans = new ArrayList<>();
        ACNode cur = root;
        for (char c : str) {
            int index = c - 'a';
            // 当前字符在当前路径上没匹配出来，沿着fail走到另外一条路径上
            while (cur.nexts[index] == null && cur != root) {
                cur = cur.fail;
            }
            // 你是真的没有还是走到头了
            // 如果真的没有就代表你这个字符一定不是敏感词的起点，直接从头开始，并由于follow != root隐式地进入了下一个循环
            // 如果你有子路径是匹配的就继续走
            cur = cur.nexts[index] != null ? cur.nexts[index] : root;
            // 沿着fail链查找
            ACNode follow = cur;
            while (follow != root) {
                // 之前这个敏感词已经出现过了
                if (follow.endUse) {
                    break;
                }
                // 只要在fail链上查找时找到一个结尾节点，那这个节点代表的敏感词就出现过
                if (follow.end != null) {
                    ans.add(follow.end);
                    follow.endUse = true;
                }
                // 遍历fail链直到到root
                follow = follow.fail;
            }
        }
        return ans;
    }
    
}

class Solution {
    
    public static void main(String[] args) {
        ACAutomaton ac = new ACAutomaton();
        
        // 传入敏感词
        ac.insert("dhe");
        ac.insert("her");
        ac.insert("fls");
        ac.insert("abk");
        ac.insert("cd");
        ac.insert("abcdheks");
        
        // 建立敏感词典
        // 设置fail指针
        ac.build();
        
        // 传入文本检查敏感词
        List<String> contains = ac.containWords("abcdhekskdjfafhasldkflskdjhwqaeruv");
        
        // 打印敏感词
        for (String word : contains) {
            System.out.println(word);
        }
    }
    
}
```

> - ***Question: 优化版***

```java
// 本题防止fail指针绕圈的方式为：
// 遍历文章时，不进行fail指针绕圈，来到的节点收集词频即可
// 遍历文章后，根据fail指针建立反图(其实是一颗树)，用树的遍历来汇总每个节点的词频
import java.io.*;

public class Main {

    // 目标字符串的数量
    public static int MAXN = 200001;

    // 所有目标字符串的总字符数量
    public static int MAXS = 200001;

    // 记录每个目标串的结尾节点编号
    public static int[] end = new int[MAXN];

    // AC自动机
    public static int[][] tree = new int[MAXS][26];

    public static int[] fail = new int[MAXS];

    public static int cnt = 0;

    // 具体题目相关，本题为收集词频
    // 所以每个节点记录词频
    public static int[] times = new int[MAXS];

    // 可以用作队列或者栈，一个容器而已
    public static int[] box = new int[MAXS];

    // 链式前向星，为了建立fail指针的反图
    public static int[] head = new int[MAXS];

    public static int[] next = new int[MAXS];

    public static int[] to = new int[MAXS];

    public static int edge = 0;

    // 遍历fail反图，递归方法会爆栈，所以用非递归替代
    public static boolean[] visited = new boolean[MAXS];

    // AC自动机加入目标字符串
    public static void insert(int i, String str) {
        char[] s = str.toCharArray();
        int u = 0;
        for (int j = 0, c; j < s.length; j++) {
            c = s[j] - 'a';
            if (tree[u][c] == 0) {
                tree[u][c] = ++cnt;
            }
            u = tree[u][c];
        }
        // 每个目标字符串的结尾节点编号
        end[i] = u;
    }

    // 加入所有目标字符串之后
    // 设置fail指针 以及 设置直接直通表
    // 做了AC自动机固定的优化
    public static void setFail() {
        // box当做队列来使用
        int l = 0;
        int r = 0;
        for (int i = 0; i <= 25; i++) {
            if (tree[0][i] > 0) {
                box[r++] = tree[0][i];
            }
        }
        while (l < r) {
            int u = box[l++];
            for (int i = 0; i <= 25; i++) {
                if (tree[u][i] == 0) {
                    tree[u][i] = tree[fail[u]][i];
                } else {
                    fail[tree[u][i]] = tree[fail[u]][i];
                    box[r++] = tree[u][i];
                }
            }
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        int n = Integer.valueOf(in.readLine());
        // AC自动机建树
        for (int i = 1; i <= n; i++) {
            insert(i, in.readLine());
        }
        setFail();
        // 读入大文章
        char[] s = in.readLine().toCharArray();
        for (int u = 0, i = 0; i < s.length; i++) {
            u = tree[u][s[i] - 'a'];
            // 增加匹配次数
            times[u]++;
        }
        for (int i = 1; i <= cnt; i++) {
            // 根据fail指针建反图
            // 其实是一颗树
            addEdge(fail[i], i);
        }
        // 遍历fail指针建的树
        // 汇总每个节点的词频
        f2(0);
        for (int i = 1; i <= n; i++) {
            out.println(times[end[i]]);
        }
        out.flush();
        out.close();
        in.close();
    }

    public static void addEdge(int u, int v) {
        next[++edge] = head[u];
        head[u] = edge;
        to[edge] = v;
    }

    // 逻辑是对的
    // 但是递归开太多层了会爆栈
    // C++这道题不会爆栈
    // java会
    public static void f1(int u) {
        for (int i = head[u]; i > 0; i = next[i]) {
            f1(to[i]);
            times[u] += times[to[i]];
        }
    }

    // 改成非递归才能通过
    // 因为是用栈来模拟递归
    // 只消耗内存空间(box和visited)
    // 不消耗系统栈的空间
    // 所以很安全
    public static void f2(int u) {
        // box当做栈来使用
        int r = 0;
        box[r++] = u;
        int cur;
        while (r > 0) {
            cur = box[r - 1];
            if (!visited[cur]) {
                visited[cur] = true;
                for (int i = head[cur]; i > 0; i = next[i]) {
                    box[r++] = to[i];
                }
            } else {
                r--;
                for (int i = head[cur]; i > 0; i = next[i]) {
                    times[cur] += times[to[i]];
                }
            }
        }
    }

}
```
