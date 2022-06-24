# [剑指 Offer 18. 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

### 解题思路
1. 如果头节点就是要删除的值，那么就将头节点往下移，直到移到不需要删除为止
2. 创建pre指针（用来指向距离当前遍历点最近的且值为不需要删除的节点），cur指针（遍历节点）
3. 在while循环中，如果cur.val == val，那么pre指向cur的下一个，断开与当前cur的连接，不然的话pre跳到cur的位置，最后每一次遍历cur后移，循环结束返回head

### 代码

```java
// java指针操作
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    // 必须要返回新head，因为head所在的位置有可能就是要删除的位置
    public ListNode deleteNode(ListNode head, int val) {
        while (head != null) {
            if (head.val != val) {
                break;
            }
            head = head.next;
        }
        ListNode pre = head;
        ListNode cur = head;
        while (cur != null) {
            if (cur.val == val) {
                pre.next = cur.next;
            } else {
                pre = cur;
            }
            cur = cur.next;
        }
        return head;
    }
}
```
