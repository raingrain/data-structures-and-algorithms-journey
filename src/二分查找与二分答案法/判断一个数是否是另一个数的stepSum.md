# 判断一个数是否是另一个数的stepSum

## 左老师课堂讲述

> - ***Question***
>   - 对于数字 `680` ，有 `680 + 68 + 6 = 754` ，定义 `680` 的 `stepSum` 为 `754` 。
>   - 给定一个正数 `num` ，判断它是不是某一个数的 `stepSum` 。

---

## *Java*

> - ***二分***
>   - 有两个数 `a, b` ，它们对应的步骤和为 `A, B` ，若 `a > b` ，则 `A > B` 。
>   - 发现单调性以后直接使用二分查找即可。

```java
class Solution {
    
    public static boolean isStepSum(int num) {
        int left = 0;
        int right = num;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int cur = getStepSum(mid);
            if (cur == num) {
                return true;
            } else if (cur < num) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return false;
    }
    
    private static int getStepSum(int num) {
        int sum = 0;
        while (num != 0) {
            sum += num;
            num /= 10;
        }
        return sum;
    }
    
}
```
