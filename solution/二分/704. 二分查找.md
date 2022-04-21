# [二分查找](https://leetcode-cn.com/problems/binary-search/)

### 解题思路
1. 不论是循环法还是递归法，终止条件都是right小于left，mid的计算方式都是(left + right) // 2（关键！！！坑！），跳转时想好时闭区间还是开区间，我是闭区间，需要mid +- 1

### 代码

```python3 []
# python非递归版本

class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1
        mid = 0
        while left <=  right:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                left = mid + 1
            elif nums[mid] > target:
                right = mid - 1
        return -1
```

```python3 []
# python递归版本

class Solution:
    def search(self, nums: List[int], target: int) -> int:
        return self.recursion(nums, target, 0, len(nums) - 1)
    
    def recursion(self, nums, target, left, right):
        if left > right:
            return -1
        else:
            mid = (left + right) // 2
            if nums[mid] == target:
                return mid
            else:
                if nums[mid] > target:
                    return self.recursion(nums, target, left, mid - 1)
                else:
                    return self.recursion(nums, target, mid + 1, right)
```
