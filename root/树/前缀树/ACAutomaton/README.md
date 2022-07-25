# [AC自动机]()

### *Java*

> - ***AC自动机***
>   - 一种基于前缀树和KMP算法改进而来的用于在大文本中快速查找敏感词的数据结构
>   - 关键的改进在于构建完前缀树后引入的fail指针以及fail指针的连接和基于fail指针的查找规则
>   - 基于fail指针的跳转策略继承了KMP算法中“忽略”的思想，即我一定知道你不是我要匹配的字符串的起点（前缀到后缀起点之间的字符），我直接忽略你
>   - 并且fail保证了忽略的严格性，即为了不错过任何一个字符尽可能少的忽略掉字符
>   - 具体过程看代码

```java
class Solution {

    public static void main(String[] args) {
        ACAutomaton ac = new ACAutomaton();

        // 构造敏感词典
        ac.insert("dhe");
        ac.insert("her");
        ac.insert("fls");
        ac.insert("abk");
        ac.insert("cd");
        ac.insert("abcdheks");

        // 设置fail指针
        ac.build();

        List<String> contains = ac.containWords("abcdhekskdjfafhasldkflskdjhwqaeruv");
        for (String word : contains) {
            System.out.println(word);
        }
    }
}


class Node {

    // 当前节点之前的路径有一个结束了的敏感词
    public String end;
    // 当前节点之前的路径代表的敏感词在文本中是否被找到过
    public boolean endUse;
    // 前缀树最大的改动
    public Node fail;
    // 我有几条子路径
    // 这里用26个小写英文字母来表示文本
    // 如果不限制的话可以使用HashSet
    public Node[] nexts;

    // 初始都是空或false
    public Node() {
        end = null;
        endUse = false;
        fail = null;
        nexts = new Node[26];
    }
}


class ACAutomaton {

    // 前缀树的根节点
    public Node root;


    // 建立好根节点
    public ACAutomaton() {
        root = new Node();
    }

    // 往前缀树中插入敏感词s
    public void insert(String s) {
        char[] str = s.toCharArray();
        // 用于遍历的cur
        Node cur = root;
        // 出现的index都是用来换算小写字母在长度为26的数组中的位置
        int index = 0;
        for (int i = 0; i < str.length; i++) {
            index = str[i] - 'a';
            // 如果我的子路径中没有你我就建一个
            if (cur.nexts[index] == null) {
                cur.nexts[index] = new Node();
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
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        Node cur = root;
        Node curFail = null;
        while (!queue.isEmpty()) {
            // 我们在一个节点弹出时将其子节点的fail指针挂载好
            // 因为如果弹出时挂自己的就没法找到自己的父节点
            cur = queue.poll();
            for (int i = 0; i < 26; i++) {
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
        Node cur = root;
        // 沿着fail链查找
        Node follow = null;
        int index = 0;
        for (int i = 0; i < str.length; i++) {
            index = str[i] - 'a';
            // 当前字符在当前路径上没匹配出来，沿着fail走到另外一条路径上
            while (cur.nexts[index] == null && cur != root) {
                cur = cur.fail;
            }
            // 你是真的没有还是走到头了
            // 如果真的没有就代表你这个字符一定不是敏感词的起点，直接从头开始，并由于follow != root隐式地进入了下一个循环
            // 如果你有子路径是匹配的就继续走
            cur = cur.nexts[index] != null ? cur.nexts[index] : root;
            follow = cur;
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
```
