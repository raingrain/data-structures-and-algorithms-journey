# N叉树的序列化与反序列化

## [428. 序列化和反序列化 N 叉树](https://leetcode.cn/problems/serialize-and-deserialize-n-ary-tree/)

> - ***Question***
>   - 序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。
>   - 请设计一个算法来实现N叉树的序列化与反序列化。这里不限定你的序列/反序列化算法执行逻辑，你只需要保证一个N叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。
>   - 请实现 `Codec` 类中的 `serialize` 和 `deserialize` 方法，分别用于序列化一棵树和将一个序列反序列化成树。

---

## *Java*

> - ***DFS***

```java
class Node {
    
    public int val;
    public List<Node> children;
    
    public Node() {
        children = new ArrayList<>();
    }
    
    public Node(int _val) {
        val = _val;
        children = new ArrayList<>();
    }
    
    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
    
}

class Codec {
    
    public String serialize(Node root) {
        if (root == null) {
            // 空树！直接返回#
            return "#";
        }
        StringBuilder builder = new StringBuilder();
        serializeByRecursion(builder, root);
        return builder.toString();
    }
    
    // 当前来到head
    private void serializeByRecursion(StringBuilder builder, Node head) {
        builder.append(head.val).append(",");
        if (!head.children.isEmpty()) {
            builder.append("[,");
            for (Node next : head.children) {
                serializeByRecursion(builder, next);
            }
            builder.append("],");
        }
    }
    
    public static Node deserialize(String data) {
        if (data.equals("#")) {
            // #代表空树
            return null;
        }
        // 按逗号分割，这也是为什么孩子前要加一个逗号，如1,[,2,3]切出 1 [ 2 3 ]
        // 这在代码中可以知道左括号和右括号内的都是我的孩子
        String[] splits = data.split(",");
        ArrayDeque<String> queue = new ArrayDeque<>();
        for (String str : splits) {
            queue.offer(str);
        }
        return deserializeByRecursion(queue);
    }
    
    private static Node deserializeByRecursion(ArrayDeque<String> queue) {
        // 当前节点先建立好，当前节点肯定不是括号
        Node cur = new Node(Integer.parseInt(Objects.requireNonNull(queue.poll())));
        cur.children = new ArrayList<>();
        // 队列不为空且队列顶部是左括号，说明要进子树了
        if (!queue.isEmpty() && queue.peek().equals("[")) {
            queue.poll();
            // 当还没有遇到右括号时，去找孩子
            while (!Objects.equals(queue.peek(), "]")) {
                Node child = deserializeByRecursion(queue);
                // 孩子挂到当前头节点
                cur.children.add(child);
            }
            queue.poll();
        }
        return cur;
    }
    
}

```
