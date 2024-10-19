# 移掉 K 位数字

## [402. 移掉 K 位数字](https://leetcode.cn/problems/remove-k-digits/)

> - ***Question***
>   - 给你一个以字符串表示的非负整数 `num` 和一个整数 `k` ，移除这个数中的 `k` 位数字，使得剩下的数字最小。请你以字符串形式返回这个最小的数字。
>   - ***tips:***
>     - `1 <= k <= num.length <= 10^5`
>     - `num` 仅由若干位数字 `0 - 9` 组成
>     - 除了 `0` 本身之外 `num` 不含任何前导零

---

## *Java*

> - ***贪心 + 单调栈***

```java
import java.util.*;

class Solution {

    public String removeKdigits(String num, int k) {
        ArrayDeque<Character> deque = new ArrayDeque<>();
        int length = num.length();
        for (int i = 0; i < length; ++i) {
            char digit = num.charAt(i);
            while (!deque.isEmpty() && k > 0 && deque.peekLast() > digit) {
                deque.pollLast();
                k--;
            }
            deque.offerLast(digit);
        }

        for (int i = 0; i < k; ++i) {
            deque.pollLast();
        }

        StringBuilder ret = new StringBuilder();
        boolean leadingZero = true;
        while (!deque.isEmpty()) {
            char digit = deque.pollFirst();
            if (leadingZero && digit == '0') {
                continue;
            }
            leadingZero = false;
            ret.append(digit);
        }
        return ret.length() == 0 ? "0" : ret.toString();
    }

}
```
