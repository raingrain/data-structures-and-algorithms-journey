# AC自动机代码模板

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
    // 前缀树最大的改动
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

---

> ***last change: 2022/11/10***

---
