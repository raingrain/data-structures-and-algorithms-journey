# 前缀树Trie

## [208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

## [剑指 Offer II 062. 实现前缀树](https://leetcode.cn/problems/QC3q1f/)

> - ***Question***
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

---

## *Java*

> - ***数组或者哈希表实现前缀树***
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
```

---

> ***last change: 2023/4/19***

---
