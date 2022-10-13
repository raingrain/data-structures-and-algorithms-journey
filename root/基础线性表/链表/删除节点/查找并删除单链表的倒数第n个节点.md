# 查找并删除单链表的倒数第n个节点

# [面试题 02.02. 返回倒数第 k 个节点](https://leetcode.cn/problems/kth-node-from-end-of-list-lcci/)

# [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

# [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

# [剑指 Offer II 021. 删除链表的倒数第 n 个结点](https://leetcode.cn/problems/SLwz0R/)

> - ***Question 1***
>   - 输入一个链表，输出该链表中倒数第 `k` 个节点
> - ***Question 2***
>   - 返回单链表中倒数第 `k` 个节点的值
> - ***Question 3***
>   - 删除单链表的倒数第 `n` 个节点，并且返回链表的头节点  
> - ***tips:***
>   - 尾节点是倒数第一个节点
>   - 链表中节点的数目为 `sz`
>   - `1 <= sz <= 30`
>   - `0 <= Node.val <= 100` 
>   - `1 <= n <= sz`

---

## *Java*

> - ***顺序查找***
>   - 第一次遍历先得出链表长度
>   - 第二次遍历找到该节点
> - ***辅助栈***
>   - 第一次遍历全部入栈（哨兵守卫也入栈）
>   - 第二次遍历出给定索引次的栈，找到目标节点进行操作
> - ***快慢指针***
>   - 我们将第一个指针 `fast` 指向链表的第 `n + 1` 个节点，第二个指针 `slow` 指向链表的第一个节点，此时指针 `fast` 与 `slow` 二者之间刚好间隔 `n` 个节点。
>   - 此时两个指针同步向后走，当第一个指针 `fast` 走到链表的尾部空节点时，则此时 `slow` 指针刚好指向链表的倒数第 `n` 个节点
>   - 将 `slow` 初始为哨兵守卫，可以得到倒数第 `n` 个节点的前一个节点，利于删除操作（可以避免形如 `cur.next = cur.next.next` 中右边是否不存在的错误）


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 * int val;
 * ListNode next;
 * ListNode() {}
 * ListNode(int val) { this.val = val; }
 * ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    
    // 删除倒数第n个节点
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 栈
        return removeNthFromEndByStack(head, n);
        
        // 直接遍历
        // return removeNthFromEndByLoop(head, n);
        
        // 快慢指针
        // return removeNthFromEndBySlowAndFast(head, n);
    }
    
    // 栈
    public ListNode removeNthFromEndByStack(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode cur = dummy;
        Stack<ListNode> stack = new Stack<>();
        while (cur != null) {
            stack.push(cur);
            cur = cur.next;
        }
        for (int i = 1; i <= n; ++i) {
            cur = stack.pop();
        }
        stack.pop().next = cur.next;
        return dummy.next;
    }
    
    // 直接遍历删除
    public ListNode removeNthFromEndByLoop(ListNode head, int n) {
        ListNode cur = head;
        int length = 0;
        while (cur != null) {
            ++length;
            cur = cur.next;
        }
        ListNode dummy = new ListNode(0, head);
        cur = dummy;
        for (int i = 1; i <= length - n; ++i) {
            cur = cur.next;
        }
        cur.next = cur.next.next;
        return dummy.next;
    }
    
    // 快慢指针删除
    public ListNode removeNthFromEndBySlowAndFast(ListNode head, int n) {
        if (head.next == null) {
            return null;
        } else {
            ListNode dummy = new ListNode(0, head);
            ListNode slow = dummy;
            ListNode fast = head;
            for (int i = 1; i <= n; ++i) {
                fast = fast.next;
            }
            while (fast != null) {
                fast = fast.next;
                slow = slow.next;
            }
            slow.next = slow.next.next;
            return dummy.next;
        }
    }
    
    // 获得倒数第n个节点的值
    public int kthToLast(ListNode head, int k) {
        return getKthFromEnd(head, k).val;
    }
    
    // 获得倒数第n个节点
    public ListNode getKthFromEnd(ListNode head, int k) {
        // 循环
        return getKthFromEndByLoop(head, k);
        
        // 快慢指针
        // return getKthFromEndBySlowAndFast(head, k);
    }
    
    // 顺序查找
    public ListNode getKthFromEndByLoop(ListNode head, int k) {
        ListNode cur = head;
        int length = 0;
        while (cur != null) {
            ++length;
            cur = cur.next;
        }
        cur = head;
        for (int i = 1; i <= length - k; ++i) {
            cur = cur.next;
        }
        return cur;
    }
    
    // 快慢指针
    public ListNode getKthFromEndBySlowAndFast(ListNode head, int k) {
        ListNode slow = head;
        ListNode fast = slow;
        for (int i = 1; i <= k; ++i) {
            fast = fast.next;
        }
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
    
}
```

---

> ***last change: 2022/10/13***

---
