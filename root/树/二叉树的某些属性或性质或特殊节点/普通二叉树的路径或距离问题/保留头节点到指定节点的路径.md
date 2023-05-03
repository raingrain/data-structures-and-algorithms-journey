# 保留头节点到指定节点的路径

## 左老师课堂讲述

> - ***Question***
>   - 给定一颗多叉树，上面有一些节点的 `retain` 属性被标记为 `true` ，请你返回一颗树的头节点，这颗新的树是原来的树保留了所有从头节点到该保留节点的路径而形成的。

---

## *Java*

> - ***树形DP***

```java
class Solution {
    
    private static class Node {
        
        // 值
        public int value;
        // 是否保留，原树中true代表保留了
        public boolean retain;
        // 下级节点
        public List<Node> nexts;
        
        public Node(int value, boolean retain) {
            this.value = value;
            this.retain = retain;
            nexts = new ArrayList<>();
        }
        
    }
    
    // 给定一棵树的头节点head
    // 请按照题意，保留节点，没有保留的节点删掉
    // 树调整完之后，返回头节点
    public static Node retain(Node x) {
        if (x.nexts.isEmpty()) {
            // 来到叶子节点，看看叶子节点需不需要保留
            return x.retain ? x : null;
        }
        // x下层有节点
        List<Node> newNexts = new ArrayList<>();
        for (Node next : x.nexts) {
            Node newNext = retain(next);
            if (newNext != null) {
                newNexts.add(newNext);
            }
        }
        // x.nexts  老的链表，下级节点
        // newNexts 新的链表，只有需要保留的在里面
        // 只要x下层有需要保留的或者x自己需要保留，就保留x
        // 没中第一个说明x下层没有需要保留的，所以挂上去也是空，无妨
        if (!newNexts.isEmpty() || x.retain) {
            x.nexts = newNexts;
            return x;
        }
        // x以下都不用保留
        return null;
    }
    
}
```

---

> ***last change: 2023/5/3***

---
