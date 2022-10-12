# k个一组反转链表

## [25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/)

> - ***Question***
>   - 给你链表的头节点 `head` ，每 `k` 个节点一组进行翻转，请你返回修改后的链表  
>   - `k` 是一个正整数，它的值小于或等于链表的长度  
>   - 如果节点总数不是 `k` 的整数倍，那么请将最后剩余的节点保持原有顺序  
>   - 你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换
>   - ***tips:***
>     - 链表中的节点数目为 `n`  
>     - `1 <= k <= n <= 5000`  
>     - `0 <= Node.val <= 1000`
>     - 设计一个只用 `O(1)` 额外内存空间的算法解决此问题

---

## *Java*

> - ***模拟***
>   - 将问题分成 `3` 部分：
>     - 找出长度小于等于 `k` 的一组
>     - 反转一组链表
>     - 反转完的新链表怎么与刚反转后的链表连接
>   - 具体的实现细节看代码
>   - 时间复杂度为 `O(n)` ，其中 `n` 为链表的长度。`start` 指针会在 `O(|n/k|)` 个节点上停留，每次停留需要进行一次 `O(k)` 的翻转操作  
>   - 空间复杂度为 `O(1)` ，我们只需要建立常数个变量

```Java
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
    
    public ListNode reverseKGroup(ListNode head, int k) {
        // 每一组的起点节点
        ListNode start = head;
        // 每一组的结束节点
        ListNode end = getKGroupEnd(head, k);
        // 如果整条链表的长度就没有到k个，不反转直接返回
        if (end != null) {
            // 最少有一组需要反转的链表区间，返回的新链表头部必然是第一组的最后一个节点
            head = end;
            reverse(start, end);
            // 反转好的链表的最后一个节点
            ListNode lastEnd = start;
            while (lastEnd.next != null) {
                // 新组的start和end
                start = start.next;
                end = getKGroupEnd(start, k);
                // 不齐k个直接返回
                if (end == null) {
                    return head;
                } else {
                    // 齐k个就反转
                    reverse(start, end);
                    // lastEnd指向反转后的头
                    lastEnd.next = end;
                    // lastEnd跳到新结束节点
                    lastEnd = start;
                }
            }
        }
        return head;
    }
    
    // 返回以start为头的长度为k的一组链表的最后一个节点
    public ListNode getKGroupEnd(ListNode start, int k) {
        // start != null避免还没到k个就结束了
        while (--k != 0 && start != null) {
            start = start.next;
        }
        return start;
    }
    
    // 反转start到end之间的链表
    // 结束时将start指向end的下一个（不考虑谁指向end）
    public void reverse(ListNode start, ListNode end) {
        end = end.next;
        ListNode last = null;
        ListNode cur = start;
        ListNode next = null;
        while (cur != end) {
            next = cur.next;
            cur.next = last;
            last = cur;
            cur = next;
        }
        start.next = end;
    }
    
}
```

---

> ***last change: 2022/10/12***

---
