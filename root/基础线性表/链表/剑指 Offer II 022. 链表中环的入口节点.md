# [剑指 Offer II 022. 链表中环的入口节点](https://leetcode-cn.com/problems/c32eOV/)

### 解题思路
1. 基于容器的就准备一个数组或者是集合，每次遍历到一个节点的时候就先看看集合里面有没有它，有的话它就是入环第一个节点，不然就把它加入集合中，往下遍历，遍历到空肯定无环
2. 双指针法（快慢指针）就很有技巧性了，需要记住两个结论（不用证明）：快慢指针同时从头节点开始进动，有环的话必然会在环上某一节点处相遇；相遇节点在环上进动次数为头节点到第一个入环节点的距离时必定处于第一个入环节点处
3. 直接利用结论准备两个while循环，先是快慢指针相遇在相遇节点，再是从头节点与相遇节点处开始进动并相遇在入环节点

### 代码

```java
// java基于容器或双指针
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        // return detectCycleByHashset(head);
        return detectCycleByPoint(head);
    }

    public ListNode detectCycleByHashset(ListNode head) {
        if (head == null || head.next == null) {
            return null;
        } else {
            // 创建一个哈希表，存储每一个节点
            HashSet<ListNode> set = new HashSet<ListNode>();
            ListNode cur = head;
            // 走到空一定无环
            while (cur != null) {
                // 每次加之前查询这个节点在不在哈希表中，在就返回这个节点
                if (set.contains(cur) == true) {
                    return cur;
                } else {
                    // 不然就加入哈希表
                    set.add(cur);
                    cur = cur.next;
                }
            }
            return null;
        }
    }

    public ListNode detectCycleByPoint(ListNode head) {
        // 判空以及保证快指针和慢指针在else初始化时已经出发的准确性
        if (head == null || head.next == null || head.next.next == null) {
            return null;
        } else {
            // 快慢指针同时进动，如果有环，快慢指针一定会在环上面相遇
            ListNode slowPoint = head.next;
            // 空出错if已经判断
            ListNode fastPoint = head.next.next;
            // 为什么快慢指针不从第一个开始，利于循环写终止条件
            while (slowPoint != fastPoint) {
                if (fastPoint.next == null || fastPoint.next.next == null) {
                    return null;
                }
                slowPoint = slowPoint.next;
                fastPoint = fastPoint.next.next;
            }
            // 把一个指针调到头位置，两者同时进动必定相遇在入环节点处
            slowPoint = head;
            while(slowPoint != fastPoint) {
                slowPoint = slowPoint.next;
                fastPoint = fastPoint.next;
            }
            return slowPoint;
        }
    }
}
```
