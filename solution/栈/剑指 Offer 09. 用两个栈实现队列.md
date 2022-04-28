# [剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

### 解题思路
参考[232. 用栈实现队列](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

### 代码

```java
class CQueue {

    public Stack<Integer> stackPush;
    public Stack<Integer> stackPop;

    public CQueue() {
        stackPush = new Stack<Integer>();
        stackPop = new Stack<Integer>();
    }

    private void poushToPop() {
        if (stackPop.isEmpty()) {
            while (!stackPush.isEmpty()) {
                stackPop.push(stackPush.pop());
            }
        }
    }
    
    public void appendTail(int value) {
        stackPush.push(value);
        poushToPop();
    }
    
    public int deleteHead() {
        poushToPop();
        return stackPop.isEmpty() ? -1 : stackPop.pop();
    }
}

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue obj = new CQueue();
 * obj.appendTail(value);
 * int param_2 = obj.deleteHead();
 */
```
