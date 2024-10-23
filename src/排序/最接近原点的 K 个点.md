# 最接近原点的 K 个点

## [973. 最接近原点的 K 个点](https://leetcode.cn/problems/k-closest-points-to-origin/)

> - **Question**
>   - 给定一个数组 `points` ，其中 `points[i] = [xi, yi]` 表示 `X-Y` 平面上的一个点，并且是一个整数 `k` ，返回离原点 `(0, 0)` 最近的 `k` 个点。
>   - 这里，平面上两点之间的距离是欧几里德距离 `√(x1 - x2)^2 + (y1 - y2)^2` 。
>   - 你可以按任何顺序返回答案。除了点坐标的顺序之外，答案 确保是唯一的。
>   - **Tips**
>     - `1 <= k <= points.length <= 10^4`
>     - `-10^4 < xi, yi < 10^4`

## Java

> - **自定义排序**

```java
import java.util.*;

class Solution {

    public int[][] kClosest(int[][] points, int k) {
        Arrays.sort(points, Comparator.comparingInt(point -> (point[0] * point[0] + point[1] * point[1])));
        return Arrays.copyOfRange(points, 0, k);
    }

}
```
