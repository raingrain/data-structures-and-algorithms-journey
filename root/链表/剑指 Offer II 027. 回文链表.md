# [剑指 Offer II 027. 回文链表](https://leetcode-cn.com/problems/aMhZSa/)

### 解题思路
1. 记住快慢指针的模板，可以快速地找到一个链表的中点或者上中点
2. 记住利用快慢指针找中点后找两头节点指针再用双指针从两边往中间遍历的思路，可用于一些特殊的排序和回文

### 代码

```java
// java容器或者双指针判断回文
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public boolean isPalindrome(ListNode head) {
        // 双指针
        return isPalindromeByPoint(head);

        // 双指针加栈
        // return isPalindromeByPointAndStack(head);

        // 直接使用容器
        // return isPalindromeByStack(head);
    }

    public boolean isPalindromeByPoint(ListNode head) {
        if ((head == null) || (head.next == null)) {
            return true;
        } else {
            ListNode leftPoint = head;
            ListNode rightPoint = head;
            // 快慢指针，找到中点指针（长度为奇数）或上中点指针（长度为偶数）
            while ((rightPoint.next != null) && (rightPoint.next.next != null)) {
                leftPoint = leftPoint.next;
                rightPoint = rightPoint.next.next;
            }
            rightPoint = reverseLinkedList(leftPoint.next, leftPoint);
            leftPoint.next = null;
            leftPoint = head;
            // 从两边往中间遍历链表，如果节点对应值不相等就返回false，不然因为偶数长度的缘故，leftPoint总是会先于或者与rightPoint同时碰到（上）中点的next（null），结束循环返回true
            while (leftPoint != null) {
                if (leftPoint.val != rightPoint.val) {
                    return false;
                } else {
                    leftPoint = leftPoint.next;
                    rightPoint = rightPoint.next;
                }
            }
            return true;
        }
    }

    // mid是为了指向中点而不是null
    public ListNode reverseLinkedList (ListNode head, ListNode mid) {
        ListNode pre = mid;
        ListNode next = null;
        ListNode cur = head;
        while (cur != null) {
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }

    public boolean isPalindromeByPointAndStack(ListNode head) {
        if ((head == null) || (head.next == null)) {
            return true;
        } else {
            ListNode fastPoint = head;
            ListNode slowPoint = head;
            while ((fastPoint.next != null) && (fastPoint.next.next != null)) {
                slowPoint = slowPoint.next;
                fastPoint = fastPoint.next.next;
            }
            slowPoint = slowPoint.next;
            Stack<Integer> stack = new Stack<>();
            while (slowPoint != null) {
                stack.push(slowPoint.val);
                slowPoint = slowPoint.next;
            }
            while (!stack.empty()) {
                if (stack.pop() != head.val) {
                    return false;
                }
                head = head.next;
            }
            return true;
        }
    }

    public boolean isPalindromeByStack(ListNode head) {
        Stack<ListNode> stack = new Stack<ListNode>();
        ListNode node = head;
        while (node != null) {
            stack.push(node);
            node = node.next;
        }
        node = head;
        while (node != null) {
            if (node.val != stack.pop().val) {
                return false;
            }
            node = node.next;
        }
        return true;
    }
}
```
