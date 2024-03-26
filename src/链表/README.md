# 链表

## 链表中最重要的6个指针或节点

> - 上一个节点或新链表的最后一个节点 `pre` / `last`
> - 当前需要操作的节点 `cur` / `node`  
> - 当前节点的下一个节点 `next`
> - 慢指针 `slow`
> - 快指针 `fast`
> - 哨兵守卫 `dummy` / `sentryGuard` / `dummyHead`  

## 快慢指针模板

```java
// 快慢指针模板
// 1. 在调用 next 字段之前，始终检查节点是否为空。
// 2. 仔细定义循环的结束条件。

// 假设我们每次移动较快的指针 2 步，每次移动较慢的指针 1 步。
// 如果没有循环，快指针需要 N/2 次才能到达链表的末尾，其中 N 是链表的长度。
// 如果存在循环，则快指针需要 M 次才能赶上慢指针，其中 M 是列表中循环的长度。
// 显然，M <= N 。所以我们将循环运行 N 次。对于每次循环，我们只需要常量级的时间。因此，该算法的时间复杂度总共为 O(N)。

// Initialize slow & fast pointers
ListNode slow = head;
ListNode fast = head;
/**
 * Change this condition to fit specific problem.
 * Attention: remember to avoid null-pointer error
 **/
while (slow != null && fast != null && fast.next != null) {
    slow = slow.next;           // move slow pointer one step each time
    fast = fast.next.next;      // move fast pointer two steps each time
    if (slow == fast) {         // change this condition to fit specific problem
        return true;
    }
}
return false;   // change return value to fit specific problem
```

## 单链表与双链表的区别

它们在许多操作中是相似的：

- 它们都无法在常量时间内随机访问数据。
- 它们都能够在 `O(1)` 时间内在给定结点之后或列表开头添加一个新结点。
- 它们都能够在 `O(1)` 时间内删除第一个结点。

但是删除给定结点(包括最后一个结点)时略有不同：

- 在单链表中，它无法获取给定结点的前一个结点，因此在删除给定结点之前我们必须花费 `O(N)` 时间来找出前一结点。
- 在双链表中，这会更容易，因为我们可以使用“prev”引用字段获取前一个结点。因此我们可以在 `O(1)` 时间内删除给定结点。

## 链表和其他数据结构之间时间复杂度的比较

![image](./images/时间复杂度比较.png)

如果你需要经常添加或删除结点，链表可能是一个不错的选择。

如果你需要经常按索引访问元素，数组可能是比链表更好的选择。
