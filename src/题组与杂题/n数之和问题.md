# n数之和问题

## [1. 两数之和](https://leetcode.cn/problems/two-sum/)

> - ***Question 1***
>   - 给定一个整数数组 `nums` 和一个整数目标值 `target` ，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。
>   - 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
>   - 你可以按任意顺序返回答案。
>   - ***tips:***
>     - `2 <= nums.length <= 10^4`
>     - `-10^9 <= nums[i] <= 10^9`
>     - `-10^9 <= target <= 10^9`
>     - 只会存在一个有效答案

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

## [LCR 007. 三数之和](https://leetcode.cn/problems/1fGaJU/)

> - ***Question 2***
>   - 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j, i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。
>   - 请你返回所有和为 `0` 且不重复的三元组。
>   - ***tips:***
>     - `3 <= nums.length <= 3000`
>     - `-10^5 <= nums[i] <= 10^5`

## [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/)

> - ***Question 3***
>   - 给你一个下标从 `1` 开始的整数数组 `numbers` ，该数组已按非递减顺序排列，请你从数组中找出满足相加之和等于目标数 `target` 的两个数。如果设这两个数分别是 `numbers[index1]` 和 `numbers[index2]` ，则 `1 <= index1 < index2 <= numbers.length` 。
>   - 以长度为 `2` 的整数数组 `[index1, index2]` 的形式返回这两个整数的下标 `index1` 和 `index2` 。
>   - 你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。
>   - 你所设计的解决方案必须只使用常量级的额外空间。
>   - ***tips:***
>     - `2 <= numbers.length <= 3 * 10^4`
>     - `-1000 <= numbers[i] <= 1000`
>     - `numbers` 按非递减顺序排列
>     - `-1000 <= target <= 1000`
>     - 仅存在一个有效答案

## [LCR 006. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/kLl5u1/)

> - ***Question 4***
>   - 给定一个已按照升序排列的整数数组 `numbers` ，请你从数组中找出两个数满足相加之和等于目标数 `target` 。
>   - 函数应该以长度为 `2` 的整数数组的形式返回这两个数的下标值。 `numbers` 的下标从 `0` 开始计数 ，所以答案数组应当满足 `0 <= answer[0] < answer[1] < numbers.length` 。
>   - 假设数组中存在且只存在一对符合条件的数字，同时一个数字不能使用两次。
>   - ***tips:***
>     - `2 <= numbers.length <= 3 * 10^4`
>     - `-1000 <= numbers[i] <= 1000`
>     - `numbers` 按非递减顺序排列
>     - `-1000 <= target <= 1000`
>     - 仅存在一个有效答案

## [653. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/two-sum-iv-input-is-a-bst/)

## [LCR 056. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/opLdQZ/)

> - ***Question 5***
>   - 给定一个二叉搜索树的根节点 `root` 和一个整数 `k` ，请判断该二叉搜索树中是否存在两个节点它们的值之和等于 `k` 。假设二叉搜索树中节点的值均唯一。
>   - ***tips:***
>     - 二叉树的节点个数的范围是  `[1, 10^4]`
>     - `-10^4 <= Node.val <= 10^4`
>     - `root` 为二叉搜索树
>     - `-10^5 <= k <= 10^5`

## [16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/)

> - ***Question 6***
>   - 给你一个长度为 `n` 的整数数组 `nums` 和 一个目标值 `target` 。请你从 `nums` 中选出三个整数，使它们的和与 `target` 最接近。
>   - 返回这三个数的和。
>   - 假定每组输入只存在恰好一个解。
>   - ***tips:***
>     - `3 <= nums.length <= 1000`
>     - `-1000 <= nums[i] <= 1000`
>     - `-10^4 <= target <= 10^4`

## [18. 四数之和](https://leetcode.cn/problems/4sum/)

> - ***Question 7***
>   - 给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且不重复的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：
>     - `0 <= a, b, c, d < n`
>     - `a, b, c, d` 互不相同
>     - `nums[a] + nums[b] + nums[c] + nums[d] == target`
>   - 你可以按任意顺序返回答案。
>   - ***tips:***
>     - `1 <= nums.length <= 200`
>     - `-10^9 <= nums[i] <= 10^9`
>     - `-10^9 <= target <= 10^9`

## [923. 三数之和的多种可能](https://leetcode.cn/problems/3sum-with-multiplicity/)

> - ***Question 8***
>   - 给定一个整数数组 `arr` ，以及一个整数 `target` 作为目标值，返回满足 `i < j < k` 且 `arr[i] + arr[j] + arr[k] == target` 的元组 `i, j, k` 的数量。
>   - 由于结果会非常大，请返回 `10^9 + 7` 的模。
>   - ***tips:***
>     - `3 <= arr.length <= 3000`
>     - `0 <= arr[i] <= 100`
>     - `0 <= target <= 300`

## [面试题 16.24. 数对和](https://leetcode.cn/problems/pairs-with-sum-lcci/)

> - ***Question 9***
>   - 设计一个算法，找出数组中两数之和为指定值的所有整数对。一个数只能属于一个数对。
>   - ***tips:***
>     - `nums.length <= 100000`
>     - `-10^5 <= nums[i], target <= 10^5`

## [1679. K 和数对的最大数目](https://leetcode.cn/problems/max-number-of-k-sum-pairs/)

> - ***Question 10***
>   - 给你一个整数数组 `nums` 和一个整数 `k` 。每一步操作中，你需要从数组中选出和为 `k` 的两个整数，并将它们移出数组。返回你可以对数组执行的最大操作数。
>   - ***tips:***
>     - `1 <= nums.length <= 10^5`
>     - `1 <= nums[i] <= 10^9`
>     - `1 <= k <= 10^9`

---

## *Java*

> - ***Question 1: 哈希表***
>   - 这样我们创建一个哈希表，对于每一个 `x` ，我们首先查询哈希表中是否存在 `target - x` ，然后将 `x` 插入到哈希表中，即可保证不会让 `x` 和自己匹配。
>   - 如果哈希表中有 `target - x` ，返回索引结果即可。

```java
class Solution {
    
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                return new int[]{map.get(target - nums[i]), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{-1, -1};
    }
    
}
```

> - ***Question 2: 双指针 + 两数之和***
>   - 注意这个两数之和不是 `Question 1` 的。

```java
class Solution {
    
    public List<List<Integer>> threeSum(int[] nums) {
        // 先排序
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        for (int i = nums.length - 1; i > 1; i--) {
            // 三元组最后一个数，是arr[i]   之前....二元组 + arr[i]
            if (i == nums.length - 1 || nums[i] != nums[i + 1]) {
                List<List<Integer>> twoSumAns = twoSum(nums, i - 1, -nums[i]);
                for (List<Integer> cur : twoSumAns) {
                    // 三元组从后面加，ArrayList效率高
                    cur.add(nums[i]);
                    ans.add(cur);
                }
            }
        }
        return ans;
    }
    
    // nums[0...end]这个范围上，有多少个不同二元组，相加==target，全返回
    // {-1,5}     K = 4
    // {1, 3}
    public List<List<Integer>> twoSum(int[] nums, int end, int target) {
        int left = 0;
        int right = end;
        List<List<Integer>> ans = new ArrayList<>();
        // 双指针从两边往中间遍历
        while (left < right) {
            if (nums[left] + nums[right] > target) {
                // 大于右指针左移
                right--;
            } else if (nums[left] + nums[right] < target) {
                // 小于左指针右移
                left++;
            } else {
                // 开头或者和前一个位置不一样
                if (left == 0 || nums[left - 1] != nums[left]) {
                    List<Integer> cur = new ArrayList<>();
                    cur.add(nums[left]);
                    cur.add(nums[right]);
                    ans.add(cur);
                }
                left++;
            }
        }
        return ans;
    }
    
}
```

> - ***Question 3 & Question 4: 双指针***
>   - 初始时两个指针分别指向第一个元素位置和最后一个元素的位置。每次计算两个指针指向的两个元素之和，并和目标值比较。如果两个元素之和等于目标值，则发现了唯一解。如果两个元素之和小于目标值，则将左侧指针右移一位。如果两个元素之和大于目标值，则将右侧指针左移一位。移动指针之后，重复上述操作，直到找到答案。
>   - 使用双指针的实质是缩小查找范围。那么会不会把可能的解过滤掉？答案是不会。假设 `numbers[i] + numbers[j] = target` 是唯一解，其中 `0 <= i < j <= numbers.length - 1` 。初始时两个指针分别指向下标 `0` 和下标 `numbers.length - 1` ，左指针指向的下标小于或等于 `i` ，右指针指向的下标大于或等于 `j` 。除非初始时左指针和右指针已经位于下标 `i` 和 `j` ，否则一定是左指针先到达下标 `i` 的位置或者右指针先到达下标 `j` 的位置。
>   - 如果左指针先到达下标 `i` 的位置，此时右指针还在下标 `j` 的右侧， `sum > target` ，因此一定是右指针左移，左指针不可能移到 `i` 的右侧。
>   - 如果右指针先到达下标 `j` 的位置，此时左指针还在下标 `i` 的左侧， `sum < target` ，因此一定是左指针右移，右指针不可能移到 `j` 的左侧。
>   - 由此可见，在整个移动过程中，左指针不可能移到 `i` 的右侧，右指针不可能移到 `j` 的左侧，因此不会把可能的解过滤掉。由于题目确保有唯一的答案，因此使用双指针一定可以找到答案。
>   - 时间复杂度 `O(n)` ，其中 `n` 是数组的长度。两个指针移动的总次数最多为 `n` 次。空间复杂度 `O(1)` 。

```java
class Solution {

    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                // Question 3
                // return new int[]{left + 1, right + 1};
                // Question 4
                return new int[]{left, right};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return new int[]{-1, -1};
    }

}
```

> - ***Question 5: 中序遍历 + 双指针***
>   - 注意到二叉搜索树的中序遍历是升序排列的，我们可以将该二叉搜索树的中序遍历的结果记录下来，得到一个升序数组。然后转化为 `Question 3 & Question 4` 使用双指针。

```java
class TreeNode {

    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {
    }

    TreeNode(int val) {
        this.val = val;
    }

    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }

}

class Solution {

    ArrayList<Integer> list = new ArrayList<>();

    public boolean findTarget(TreeNode root, int k) {
        inorderTraversal(root);
        int left = 0, right = list.size() - 1;
        while (left < right) {
            if (list.get(left) + list.get(right) == k) {
                return true;
            }
            if (list.get(left) + list.get(right) < k) {
                left++;
            } else {
                right--;
            }
        }
        return false;
    }

    public void inorderTraversal(TreeNode node) {
        if (node == null) {
            return;
        }
        inorderTraversal(node.left);
        list.add(node.val);
        inorderTraversal(node.right);
    }

}
```

> - ***Question 6: 排序 + 双指针***

```java
// 首先进行数组排序
// 在数组 nums 中，进行遍历，每遍历一个值利用其下标i，形成一个固定值 nums[i]
// 再使用前指针指向 start = i + 1 处，后指针指向 end = nums.length - 1 处，也就是结尾处
// 根据 sum = nums[i] + nums[start] + nums[end] 的结果，判断 sum 与目标 target 的距离，如果更近则更新结果 ans
// 同时判断 sum 与 target 的大小关系，因为数组有序，如果 sum > target 则 end--，如果 sum < target 则 start++，如果 sum == target 则说明距离为 0 直接返回结果
class Solution {

    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int ans = nums[0] + nums[1] + nums[2];
        for (int i = 0; i < nums.length; i++) {
            int start = i + 1, end = nums.length - 1;
            while (start < end) {
                int sum = nums[start] + nums[end] + nums[i];
                if (Math.abs(target - sum) < Math.abs(target - ans))
                    ans = sum;
                if (sum > target)
                    end--;
                else if (sum < target)
                    start++;
                else
                    return ans;
            }
        }
        return ans;
    }

}
```

> - ***Question 7: 排序 + 双指针***
>   - 最朴素的方法是使用四重循环枚举所有的四元组，然后使用哈希表进行去重操作，得到不包含重复四元组的最终答案。假设数组的长度是 `n` ，则该方法中，枚举的时间复杂度为 `O(n^4)` ，去重操作的时间复杂度和空间复杂度也很高，因此需要换一种思路。
>   - 为了避免枚举到重复四元组，则需要保证每一重循环枚举到的元素不小于其上一重循环枚举到的元素，且在同一重循环中不能多次枚举到相同的元素。
>   - 为了实现上述要求，可以对数组进行排序，并且在循环过程中遵循以下两点：
>     - 每一种循环枚举到的下标必须大于上一重循环枚举到的下标；
>     - 同一重循环中，如果当前元素与上一个元素相同，则跳过当前元素。
>   - 使用上述方法，可以避免枚举到重复四元组，但是由于仍使用四重循环，时间复杂度仍是 `O(n^4)` 。注意到数组已经被排序，因此可以使用双指针的方法去掉一重循环。
>   - 使用两重循环分别枚举前两个数，然后在两重循环枚举到的数之后使用双指针枚举剩下的两个数。假设两重循环枚举到的前两个数分别位于下标 `i` 和 `j` ，其中 `i < j` 。初始时，左右指针分别指向下标 `j + 1` 和下标 `n - 1` 。每次计算四个数的和，并进行如下操作：
>     - 如果和等于 `target` ，则将枚举到的四个数加到答案中，然后将左指针右移直到遇到不同的数，将右指针左移直到遇到不同的数；
>     - 如果和小于 `target` ，则将左指针右移一位；
>     - 如果和大于 `target` ，则将右指针左移一位。
>   - 使用双指针枚举剩下的两个数的时间复杂度是 `O(n)` ，因此总时间复杂度是 `O(n^3)` ，低于 `O(n^4)` 。
>   - 具体实现时，还可以进行一些剪枝操作：
>     - 在确定第一个数之后，如果 `nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target` ，说明此时剩下的三个数无论取什么值，四数之和一定大于 `target` ，因此退出第一重循环；
>     - 在确定第一个数之后，如果 `nums[i] + nums[n - 3] + nums[n - 2] + nums[n - 1] < target` ，说明此时剩下的三个数无论取什么值，四数之和一定小于 `target` ，因此第一重循环直接进入下一轮，枚举 `nums[i + 1]` ；
>     - 在确定前两个数之后，如果 `nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target` ，说明此时剩下的两个数无论取什么值，四数之和一定大于 `target` ，因此退出第二重循环；
>     - 在确定前两个数之后，如果 `nums[i] + nums[j] + nums[n - 2] + nums[n - 1] < target` ，说明此时剩下的两个数无论取什么值，四数之和一定小于 `target` ，因此第二重循环直接进入下一轮，枚举 `nums[j + 1]` 。

```java
import java.util.*;

class Solution {

    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> quadruplets = new ArrayList<List<Integer>>();
        if (nums == null || nums.length < 4) {
            return quadruplets;
        }
        Arrays.sort(nums);
        int length = nums.length;
        for (int i = 0; i < length - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            if ((long) nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target) {
                break;
            }
            if ((long) nums[i] + nums[length - 3] + nums[length - 2] + nums[length - 1] < target) {
                continue;
            }
            for (int j = i + 1; j < length - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                if ((long) nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target) {
                    break;
                }
                if ((long) nums[i] + nums[j] + nums[length - 2] + nums[length - 1] < target) {
                    continue;
                }
                int left = j + 1, right = length - 1;
                while (left < right) {
                    long sum = (long) nums[i] + nums[j] + nums[left] + nums[right];
                    if (sum == target) {
                        quadruplets.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        while (left < right && nums[left] == nums[left + 1]) {
                            left++;
                        }
                        left++;
                        while (left < right && nums[right] == nums[right - 1]) {
                            right--;
                        }
                        right--;
                    } else if (sum < target) {
                        left++;
                    } else {
                        right--;
                    }
                }
            }
        }
        return quadruplets;
    }

}
```

> - ***Question 8: 三指针***
>   - 先将数组进行排序，遍历数组下标，对于每个 `i` ，设 `T = target - A[i]` 作为剩余要凑成的目标数。接着用双指针来完成 `A[j] + A[k] == T` 的子任务。
>   - 考虑到有些元素是重复的，需要小心处理边界条件。在特殊的情况下，比如说 `target = 8` ，数组为 `[2, 2, 2, 2, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6]` ，这个数组就有大量的重复元素可以组成 `target` ，下面来分析一下这种情况该怎么处理。
>   - 只要 `A[j] + A[k] == T` ，就要算上这一对 `j, k` 组合。在这个例子里面，当 `A[j] == 2, A[k] == 6` ，有 `4 * 2 = 8` 种组合方式。
>   - 在特殊情况下，如果 `A[j] == A[k]` ，比如最后剩下的 `[4, 4, 4]` ，这里有 `3` 对。一般情况下，如果 `A[j] == A[k]` ，我们有 `M * (M - 1) / 2` 对 `(j, k)` （满足 `j < k` 且 `A[j] + A[k] == T` ）。

```java
import java.util.*;

class Solution {

    public int threeSumMulti(int[] A, int target) {
        int MOD = 1_000_000_007;
        long ans = 0;
        Arrays.sort(A);
        for (int i = 0; i < A.length; ++i) {
            int T = target - A[i];
            int j = i + 1, k = A.length - 1;

            while (j < k) {
                // These steps proceed as in a typical two-sum.
                if (A[j] + A[k] < T)
                    j++;
                else if (A[j] + A[k] > T)
                    k--;
                else if (A[j] != A[k]) {
                    int left = 1, right = 1;
                    while (j + 1 < k && A[j] == A[j + 1]) {
                        left++;
                        j++;
                    }
                    while (k - 1 > j && A[k] == A[k - 1]) {
                        right++;
                        k--;
                    }
                    ans += left * right;
                    ans %= MOD;
                    j++;
                    k--;
                } else {
                    ans += (k - j + 1) * (k - j) / 2;
                    ans %= MOD;
                    break;
                }
            }
        }
        return (int) ans;
    }

}
```

> - ***Question 9: 双指针***

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class Solution {

    public List<List<Integer>> pairSums(int[] nums, int target) {
        // 对数组进行排序
        Arrays.sort(nums);
        List<List<Integer>> ans = new ArrayList<>();
        int left = 0, right = nums.length - 1;
        while (left < right) {
            // 两个指针所指的两个元素和
            int sum = nums[left] + nums[right];
            // 如果两个的和小于目标值，那么left指针向右走一步继续寻找
            if (sum < target) {
                ++left;
            } else if (sum > target) {
                // 如果两个的和大于目标值，那么right指针向左走一步继续寻找
                --right;
            } else {
                // 如果刚好等于要找的target值，那么加入结果集中，并且left指针和right指针分别向右和向左走一步(因为一个数只能属于一个数对)
                ans.add(Arrays.asList(nums[left++], nums[right--]));
            }

        }
        return ans;
    }

}
```

> - ***Question 10: 排序 + 双指针***

```java
import java.util.*;

class Solution {

    public int maxOperations(int[] nums, int k) {
        int len = nums.length;
        int left = 0;
        int right = 0;
        Arrays.sort(nums);
        right = len - 1;
        int sum = 0;
        while (left < right) {
            if (nums[left] + nums[right] == k) {
                sum++;
                left++;
                right--;
            } else if (nums[left] + nums[right] < k) {
                left++;
            } else {
                right--;
            }


        }
        return sum;

    }

}
```
