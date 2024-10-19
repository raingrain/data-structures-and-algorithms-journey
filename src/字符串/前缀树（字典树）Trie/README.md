# 前缀树Trie

## [208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

## [剑指 Offer II 062. 实现前缀树](https://leetcode.cn/problems/QC3q1f/)

## [1804. 实现 Trie （前缀树） II](https://leetcode.cn/problems/implement-trie-ii-prefix-tree/)

> - ***Question 1***
>   - `Trie` （发音类似 "try"）或者说前缀树是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。
>   - 请你实现 `Trie` 类：
>     - `Trie()` 初始化前缀树对象。
>     - `void insert(String word)` 向前缀树中插入字符串 `word` 。
>     - `boolean search(String word)` 如果字符串 `word` 在前缀树中，返回 `true`（即在检索之前已经插入）；否则，返回 `false` 。
>     - `boolean startsWith(String prefix)` 如果之前已经插入的字符串 word 的前缀之一为 `prefix` ，返回 `true` ；否则，返回 `false` 。
>   - ***tips:***
>     - `1 <= word.length, prefix.length <= 2000`
>     - `word` 和 `prefix` 仅由小写英文字母组成
>     - `insert` 、 `search` 和 `startsWith` 调用次数 总计不超过 `3 * 10^4` 次

## [字典树的实现](https://www.nowcoder.com/practice/7f8a8553ddbf4eaab749ec988726702b)

> - ***Question 2***
>   - 字典树又称为前缀树或者 `Trie` 树，是处理字符串常用的数据结构。假设组成所有单词的字符仅是 `a ~ z` ，请实现字典树的结构，并包含以下四个主要的功能：
>     - `void insert(String word)` ：添加 `word` ，可重复添加；
>     - `void delete(String word)` ：删除 `word` ，如果 `word` 添加过多次，仅删除一次；
>     - `boolean search(String word)` ：查询 `word` 是否在字典树中出现过（完整的出现过，前缀式不算）；
>     - `int prefixNumber(String pre)` ：返回以字符串 `pre` 作为前缀的单词数量。
>   - 现在给定一个 `m` ，表示有 `m` 次操作，每次操作都为以上四种操作之一。每次操作会给定一个整数 `op` 和一个字符串 `word` ， `op` 代表一个操作码，如果 `op` 为 `1` ，则代表添加 `word` ， `op` 为 `2` 则代表删除 `word` ， `op` 为 `3` 则代表查询 `word` 是否在字典树中， `op` 为 `4` 代表返回以 `word` 为前缀的单词数量（数据保证不会删除不存在的word）。
>   - ***输入描述***
>     - 输入包含多行，第一行一个整数 `m` ，代表操作次数。接下来 `m` 行，每行包含一个整数 `op` ，和一个字符串 `word` 。
>   - ***输出描述***
>     - 对于每次操作，如果 `op` 为 `3` 时，如果 `word` 在字典树中，请输出 `YES` ，否则输出 `NO` ；如果 `op` 为 `4` 时，请输出返回以 `word` 为前缀的单词数量，其它情况不输出。
>   - ***tips:***
>     - `1 <= m <= 10^5`
>     - `1 <= op <= 4`
>     - `1 <= word.length <= 20`

---

## *Java*

> - ***Question 1: 数组或者哈希表实现前缀树***
>   - 代码中多实现了删除一个单词 `void delete(String word)` ，查询前缀 or 单词个数 `int[] searchPrefixAndWordNumber(String s)` 等功能。
>   - 看懂了插入函数部分的代码，其他都很好理解，都是一个模板的。
>   - 只有英文字母就用数组，因为 `ASCII` 可以和索引对应起来，否则就用哈希表，直接用 `ASCII` 码作为 `key` 。

```java
class TrieNode {
    
    // 自顶向下到达该节点的唯一路径作为前缀出现的次数
    public int pass;
    // 自顶向下到达该节点的唯一路径所对应的字符串的个数
    public int end;
    // 如果是英文字母就可以用数组代替，不然要用到哈希表
    public TrieNode[] nexts;
    // public HashMap<Integer, TrieNode> nexts;
    
    public TrieNode() {
        this.pass = 0;
        this.end = 0;
        this.nexts = new TrieNode[26];
        // this.nexts = new HashMap<>();
    }
    
}

class Trie {
    
    // 根节点
    private final TrieNode root;
    
    /**
     * Initialize your data structure here.
     */
    public Trie() {
        this.root = new TrieNode();
    }
    
    /**
     * Inserts a word into the trie.
     */
    public void insert(String word) {
        if (word == null) {
            return;
        }
        char[] str = word.toCharArray();
        TrieNode node = root;
        // 经过一次，加1
        ++root.pass;
        int path;
        for (char c : str) {
            // ASCII码换算索引
            path = c - 'a';
            // 没有路径就新建立
            if (node.nexts[path] == null) {
                node.nexts[path] = new TrieNode();
            }
            // 往下走，经过一次
            node = node.nexts[path];
            ++node.pass;
            // 哈希表版
            // 直接以ASCII码作为key
            /*path = c;
            if (!node.nexts.containsKey(path)) {
                node.nexts.put(path, new Node());
            }
            node = node.nexts.get(path);*/
        }
        ++node.end;
    }
    
    /**
     * Returns if the word is in the trie.
     */
    public boolean search(String word) {
        return searchWordNumber(word) != 0;
        // return searchPrefixAndWordNumber(word)[1] != 0;
    }
    
    // 查找一个字符串集中单词word出现的次数
    public int searchWordNumber(String word) {
        if (word == null) {
            return 0;
        }
        char[] str = word.toCharArray();
        TrieNode node = root;
        int path = 0;
        for (char c : str) {
            path = c - 'a';
            if (node.nexts[path] == null) {
                return 0;
            }
            node = node.nexts[path];
        }
        return node.end;
    }
    
    /**
     * Returns if there is any word in the trie that starts with the given prefix.
     */
    public boolean startsWith(String prefix) {
        return searchPrefixNumber(prefix) != 0;
        // return searchPrefixAndWordNumber(prefix)[0] != 0;
    }
    
    // 查找一个字符串集中以prefix作为前缀的次数
    public int searchPrefixNumber(String prefix) {
        if (prefix == null) {
            return 0;
        }
        char[] str = prefix.toCharArray();
        TrieNode node = root;
        int path = 0;
        for (char c : str) {
            path = c - 'a';
            if (node.nexts[path] == null) {
                return 0;
            }
            node = node.nexts[path];
        }
        return node.pass;
    }
    
    // 查找一个字符串集中以s作为前缀和单词s出现的次数
    public int[] searchPrefixAndWordNumber(String s) {
        if (s == null) {
            return new int[]{0, 0};
        }
        char[] str = s.toCharArray();
        TrieNode node = root;
        int path = 0;
        for (char c : str) {
            path = c - 'a';
            // 没路走就是字符串集中没有前缀和单词
            if (node.nexts[path] == null) {
                return new int[]{0, 0};
            }
            node = node.nexts[path];
        }
        return new int[]{node.pass, node.end};
    }
    
    // 删除一个节点并对删除后再也不会到达的路径进行删除，释放内存
    public void delete(String word) {
        if (word == null) {
            return;
        }
        if (!search(word)) {
            char[] str = word.toCharArray();
            TrieNode node = root;
            --node.pass;
            int path = 0;
            for (char c : str) {
                path = c - 'a';
                // 删除时要删除经过次数，如果经过次数变成0，释放后续路径内存，直接返回
                if (--node.nexts[path].pass == 0) {
                    node.nexts[path] = null;
                    return;
                }
                // 哈希表版
                /*if (--node.nexts.get(path).pass == 0) {
                    node.nexts.remove(path);
                    return;
                }*/
                node = node.nexts[path];
            }
            // 能走到最后，以该路径为整个单词的个数减一
            --node.end;
        }
    }
    
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */

// leetcode 1804
class Solution {

    // 路是数组实现的
    // 提交时把类名、构造方法改为Trie
    class Trie1 {

        class TrieNode {

            public int pass;
            public int end;
            public TrieNode[] nexts;

            public TrieNode() {
                pass = 0;
                end = 0;
                nexts = new TrieNode[26];
            }

        }

        private final TrieNode root;

        public Trie1() {
            root = new TrieNode();
        }

        public void insert(String word) {
            TrieNode node = root;
            node.pass++;
            for (int i = 0, path; i < word.length(); i++) { // 从左往右遍历字符
                path = word.charAt(i) - 'a'; // 由字符，对应成走向哪条路
                if (node.nexts[path] == null) {
                    node.nexts[path] = new TrieNode();
                }
                node = node.nexts[path];
                node.pass++;
            }
            node.end++;
        }

        // 如果之前word插入过前缀树，那么此时删掉一次
        // 如果之前word没有插入过前缀树，那么什么也不做
        public void erase(String word) {
            if (countWordsEqualTo(word) > 0) {
                TrieNode node = root;
                node.pass--;
                for (int i = 0, path; i < word.length(); i++) {
                    path = word.charAt(i) - 'a';
                    if (--node.nexts[path].pass == 0) {
                        node.nexts[path] = null;
                        return;
                    }
                    node = node.nexts[path];
                }
                node.end--;
            }
        }

        // 查询前缀树里，word单词出现了几次
        public int countWordsEqualTo(String word) {
            TrieNode node = root;
            for (int i = 0, path; i < word.length(); i++) {
                path = word.charAt(i) - 'a';
                if (node.nexts[path] == null) {
                    return 0;
                }
                node = node.nexts[path];
            }
            return node.end;
        }

        // 查询前缀树里，有多少单词以pre做前缀
        public int countWordsStartingWith(String pre) {
            TrieNode node = root;
            for (int i = 0, path; i < pre.length(); i++) {
                path = pre.charAt(i) - 'a';
                if (node.nexts[path] == null) {
                    return 0;
                }
                node = node.nexts[path];
            }
            return node.pass;
        }

    }

    // 路是哈希表实现的
    // 提交时把类名、构造方法改为Trie
    class Trie2 {

        class TrieNode {

            public int pass;
            public int end;
            HashMap<Integer, TrieNode> nexts;

            public TrieNode() {
                pass = 0;
                end = 0;
                nexts = new HashMap<>();
            }

        }

        private final TrieNode root;

        public Trie2() {
            root = new TrieNode();
        }

        public void insert(String word) {
            TrieNode node = root;
            node.pass++;
            for (int i = 0, path; i < word.length(); i++) { // 从左往右遍历字符
                path = word.charAt(i);
                if (!node.nexts.containsKey(path)) {
                    node.nexts.put(path, new TrieNode());
                }
                node = node.nexts.get(path);
                node.pass++;
            }
            node.end++;
        }

        public void erase(String word) {
            if (countWordsEqualTo(word) > 0) {
                TrieNode node = root;
                TrieNode next;
                node.pass--;
                for (int i = 0, path; i < word.length(); i++) {
                    path = word.charAt(i);
                    next = node.nexts.get(path);
                    if (--next.pass == 0) {
                        node.nexts.remove(path);
                        return;
                    }
                    node = next;
                }
                node.end--;
            }
        }

        public int countWordsEqualTo(String word) {
            TrieNode node = root;
            for (int i = 0, path; i < word.length(); i++) {
                path = word.charAt(i);
                if (!node.nexts.containsKey(path)) {
                    return 0;
                }
                node = node.nexts.get(path);
            }
            return node.end;
        }

        public int countWordsStartingWith(String pre) {
            TrieNode node = root;
            for (int i = 0, path; i < pre.length(); i++) {
                path = pre.charAt(i);
                if (!node.nexts.containsKey(path)) {
                    return 0;
                }
                node = node.nexts.get(path);
            }
            return node.pass;
        }

    }

}
```

> - ***Question 2***

```java
// 牛客版本
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.util.Arrays;

public class Main {

    // 如果将来增加了数据量，就改大这个值
    public static int MAXN = 150001;

    public static int[][] tree = new int[MAXN][26];

    public static int[] end = new int[MAXN];

    public static int[] pass = new int[MAXN];

    public static int cnt;

    public static void build() {
        cnt = 1;
    }

    public static void insert(String word) {
        int cur = 1;
        pass[cur]++;
        for (int i = 0, path; i < word.length(); i++) {
            path = word.charAt(i) - 'a';
            if (tree[cur][path] == 0) {
                tree[cur][path] = ++cnt;
            }
            cur = tree[cur][path];
            pass[cur]++;
        }
        end[cur]++;
    }

    public static int search(String word) {
        int cur = 1;
        for (int i = 0, path; i < word.length(); i++) {
            path = word.charAt(i) - 'a';
            if (tree[cur][path] == 0) {
                return 0;
            }
            cur = tree[cur][path];
        }
        return end[cur];
    }

    public static int prefixNumber(String pre) {
        int cur = 1;
        for (int i = 0, path; i < pre.length(); i++) {
            path = pre.charAt(i) - 'a';
            if (tree[cur][path] == 0) {
                return 0;
            }
            cur = tree[cur][path];
        }
        return pass[cur];
    }

    public static void delete(String word) {
        if (search(word) > 0) {
            int cur = 1;
            for (int i = 0, path; i < word.length(); i++) {
                path = word.charAt(i) - 'a';
                if (--pass[tree[cur][path]] == 0) {
                    tree[cur][path] = 0;
                    return;
                }
                cur = tree[cur][path];
            }
            end[cur]--;
        }
    }

    public static void clear() {
        for (int i = 1; i <= cnt; i++) {
            Arrays.fill(tree[i], 0);
            end[i] = 0;
            pass[i] = 0;
        }
    }

    public static int m, op;

    public static String[] splits;

    public static void main(String[] args) throws IOException {
        BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        String line = null;
        while ((line = in.readLine()) != null) {
            build();
            m = Integer.parseInt(line);
            for (int i = 1; i <= m; i++) {
                splits = in.readLine().split(" ");
                op = Integer.parseInt(splits[0]);
                if (op == 1) {
                    insert(splits[1]);
                } else if (op == 2) {
                    delete(splits[1]);
                } else if (op == 3) {
                    out.println(search(splits[1]) > 0 ? "YES" : "NO");
                } else if (op == 4) {
                    out.println(prefixNumber(splits[1]));
                }
            }
            clear();
        }
        out.flush();
        in.close();
        out.close();
    }

}
```
