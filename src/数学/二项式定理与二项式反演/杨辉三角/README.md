# 杨辉三角

> 具体性质可参考[维基百科](https://zh.wikipedia.org/zh-hans/%E6%9D%A8%E8%BE%89%E4%B8%89%E8%A7%92%E5%BD%A2)或[百度百科](https://baike.baidu.com/item/%E6%9D%A8%E8%BE%89%E4%B8%89%E8%A7%92/215098)。

## [118. 杨辉三角](https://leetcode.cn/problems/pascals-triangle/)

> - ***Question 1***
>   - 给定一个非负整数 `numRows` ，生成杨辉三角的前 `numRows` 行。
>   - 在杨辉三角中，每个数是它左上方和右上方的数的和。
>   - ***tips:***
>     - `1 <= numRows <= 30`

## [119. 杨辉三角 II](https://leetcode.cn/problems/pascals-triangle-ii/)

> - ***Question 2***
>   - 给定一个非负索引 `rowIndex` ，返回杨辉三角的第 `rowIndex` 行。
>   - 在杨辉三角中，每个数是它左上方和右上方的数的和。
>   - ***tips:***
>     - `0 <= rowIndex <= 33`

---

## *Java*

> - ***Question 1: 数学规律***
>   - ![image](杨辉三角左对齐.jpg)

```java
class Solution {
    
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = 0; i < numRows; i++) {
            ans.add(new ArrayList<>());
            // 最左侧的1
            ans.get(i).add(1);
        }
        for (int i = 1; i < numRows; i++) {
            for (int j = 1; j < i; j++) {
                // 普遍位置等于上面加左上角
                ans.get(i).add(ans.get(i - 1).get(j - 1) + ans.get(i - 1).get(j));
            }
            // 加上最右侧的1
            ans.get(i).add(1);
        }
        return ans;
    }
    
}
```

> - ***Question 2: 滚动数组***

```java
class Solution {
    
    public List<Integer> getRow(int rowIndex) {
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i <= rowIndex; i++) {
            for (int j = i - 1; j > 0; j--) {
                // 每一层从右往左填
                ans.set(j, ans.get(j - 1) + ans.get(j));
            }
            // 加1
            ans.add(1);
        }
        return ans;
    }
    
}
```
