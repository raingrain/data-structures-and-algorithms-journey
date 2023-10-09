# 合并k个升序链表

## [23. 合并K个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/)

## [剑指 Offer II 078. 合并排序链表](https://leetcode.cn/problems/vvXgSW/)

## [合并k个已排序的链表](https://www.nowcoder.com/practice/65cfde9e5b9b4cf2b6bafa5f3ef33fa6)

> - ***Question***
>   - 给你一个链表数组，数组中的每一个元素为一个链表的头节点，每个链表都已经按升序排列。
>   - 请你将所有链表合并到一个升序链表中，返回合并后的链表。
>   - ***tips:***
>     - `k == lists.length`
>     - `0 <= k <= 10 ^ 4`  
>     - `0 <= lists[i].length <= 500`  
>     - `-10 ^ 4 <= lists[i][j] <= 10 ^ 4`
>     - `lists[i]` 按升序排列
>     - `lists[i].length` 的总和不超过 `10 ^ 4`

---

## *Java*

> - ***小根堆***
>   - 准备一个小根堆，存储元素为 `ListNode` ，以 `ListNode` 的 `val` 作为比较基准。
>   - 我们先把所有链表头放入小根堆，由于各个链表有序，此时弹出来的节点必然是最小的，即新链表的头 `head` 。
>   - 抓住新链表的头后记得把其子链表的头扔进去，不然就会因为这段子链表不参与排序而出错。
>   - 用一个 `pre` 指针指向已经排好序的新链表的最后一个元素。
>   - 遍历小根堆，直到小根堆为空：
>     - 每次遍历都弹出一个值 `cur` 。
>     - `pre` 指向 `cur` 并跳到 `cur` 。
>     - 把 `cur` 的子链表的头加入到小根堆。
>   - 返回 `head` 。

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
    
    public static class Compare implements Comparator<ListNode> {
        
        // 泛型类型为比较函数传入参数类型
        @Override
        public int compare(ListNode o1, ListNode o2) {
            return o1.val - o2.val;
        }
        
    }
    
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null) {
            return null;
        } else {
            PriorityQueue<ListNode> heap = new PriorityQueue<>(new Compare());
            for (ListNode list : lists) {
                if (list != null) {
                    heap.add(list);
                }
            }
            // [null, null...]
            if (heap.isEmpty()) {
                return null;
            } else {
                ListNode head = heap.poll();
                ListNode pre = head;
                if (pre.next != null) {
                    heap.add(pre.next);
                }
                while (!heap.isEmpty()) {
                    ListNode cur = heap.poll();
                    pre.next = cur;
                    pre = cur;
                    if (cur.next != null) {
                        heap.add(cur.next);
                    }
                }
                return head;
            }
        }
    }
    
}

// 牛客，题意一样，输入不同
public class Main {

    // 不要提交这个类
    public static class ListNode {

        public int val;
        public ListNode next;

    }

    // 提交以下的方法
    public static ListNode mergeKLists(ArrayList<ListNode> arr) {
        // 小根堆
        PriorityQueue<ListNode> heap = new PriorityQueue<>((a, b) -> a.val - b.val);
        for (ListNode h : arr) {
            // 遍历所有的头！
            if (h != null) {
                heap.add(h);
            }
        }
        if (heap.isEmpty()) {
            return null;
        }
        // 先弹出一个节点，做总头部
        ListNode h = heap.poll();
        ListNode pre = h;
        if (pre.next != null) {
            heap.add(pre.next);
        }
        while (!heap.isEmpty()) {
            ListNode cur = heap.poll();
            pre.next = cur;
            pre = cur;
            if (cur.next != null) {
                heap.add(cur.next);
            }
        }
        return h;
    }

}
```

---

> ***last change: 2023/10/9***

---
