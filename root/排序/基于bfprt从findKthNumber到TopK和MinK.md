# 基于bfprt从findKthNumber到TopK和MinK

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

## [剑指 Offer II 076. 数组中的第 k 大的数字](https://leetcode.cn/problems/xx4gT2/)

## [703. 数据流中的第 K 大元素](https://leetcode.cn/problems/kth-largest-element-in-a-stream/)

## [剑指 Offer II 059. 数据流的第 K 大数值](https://leetcode.cn/problems/jBjn9C/)

## [面试题 17.14. 最小K个数](https://leetcode.cn/problems/smallest-k-lcci/)

## [剑指 Offer 40. 最小的k个数](https://leetcode.cn/problems/zui-xiao-de-kge-shu-lcof/)

> - ***Question 1***
>   - 给定整数数组 `nums` 和整数 `k` ，请返回数组中第 `k` 个最大的元素。
>   - 请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。
>   - 你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
>   - ***tips:***
>     - `1 <= k <= nums.length <= 10^5`
>     - `-10^4 <= nums[i] <= 10^4`
> - ***Question 2***
>   - 设计一个找到数据流中第 `k` 大元素的类。注意是排序后的第 `k` 大元素，不是第 `k` 个不同的元素。
>   - 请实现 `KthLargest` 类：
>     - `KthLargest(int k, int[] nums)` 使用整数 `k` 和整数流 `nums` 初始化对象。
>     - `int add(int val)` 将 `val` 插入数据流 `nums` 后，返回当前数据流中第 `k` 大的元素。
>   - ***tips:***
>     - `1 <= k <= 104`
>     - `0 <= nums.length <= 104`
>     - `-104 <= nums[i] <= 104`
>     - `-104 <= val <= 104`
>     - 最多调用 `add` 方法 `104` 次
>     - 题目数据保证，在查找第 `k` 大元素时，数组中至少有 `k` 个元素
> - ***Question 3***
>   - 找出数组中最小的 `k` 个数。以任意顺序返回这 `k` 个数均可。

---

## *Java*

> - ***大根堆***
>   - 先把数组中的前 `k` 个数加入到堆中。然后对于剩下的数，比堆中最大的数（堆顶）小的就加入到堆中并把原来的堆顶弹出，遍历完一遍数组后的堆顶就是第 `k` 小。
>   - 或者先全部加到堆中，在弹出至只剩下 `k` 个数
> - ***快速排序递归 or 迭代***
>   - 由于快排的每次分治都会返回等于区的左右边界，而在一个有序数组中第 `k` 小的位置是确定的并且快排从中间往两边有序，所以我们可以利用等于区来找到第 `k` 小的数，当 `k - 1` 落在等于区时就返回等于区的值。
> - ***bfprt算法***
>   - 只对于怎么选择基准值作为等于区的值做了改进：把数组分为多个长度为 `5` 的小段，不足 `5` 的自成一段，把每一段的中位数（长度为 `5` ，直接使用插入排序）拿出来作为一个中位数数组，在选择这个中位数数组的中位数（调用快排的递归函数来求）作为基准值，这样确保了每一次排序等于区左右两边的数的数量在 `3/10N` 到 `7/10N` 之间，解决了随机挑选基准值带来的极端情况。
> - 直接遍历一遍的时间复杂度再找是 `O(N*logN)` ，自底向上建堆再遍历一遍的时间复杂度是 `O(N+K*logN)` ，用 `bfprt` 搞就是 `O(N+K*logK)` 。

```java
class Solution {
    
    // 找到数组中前k大的数字，不要求顺序
    public int[] mostK(int[] arr, int k) {
        if (arr == null || arr.length == 0 || k == 0) {
            return new int[0];
        }
        int[] copy = new int[arr.length];
        System.arraycopy(arr, 0, copy, 0, arr.length);
        int KthLargestNumber = findKthLargest(arr, k);
        int[] ans = new int[k];
        int index = 0;
        // 如果数组中元素互不相同，我们可以一遍遍历过
        /*for (int num : copy) {
            if (num >= KthLargestNumber) {
                ans[index++] = num;
            }
        }*/
        // 如果数组中有相同元素，我们需要先遍历一遍计算出比第k小的数的值小的数有几个，假定k为100，找到了90个比第k小的数小的数，那么剩下10个全为k
        for (int num : copy) {
            if (num > KthLargestNumber) {
                ans[index++] = num;
            }
        }
        // 再遍历一遍，只取前10个k即可
        while (index < ans.length) {
            ans[index++] = KthLargestNumber;
        }
        return ans;
    }
    
    // 找到数组前k小的数字，不要求顺序
    public int[] smallestK(int[] arr, int k) {
        if (arr == null || arr.length == 0 || k == 0) {
            return new int[0];
        }
        int[] copy = new int[arr.length];
        System.arraycopy(arr, 0, copy, 0, arr.length);
        int KthLeastNumber = findKthLeast(arr, k);
        int[] ans = new int[k];
        int index = 0;
        for (int num : copy) {
            if (num < KthLeastNumber) {
                ans[index++] = num;
            }
        }
        while (index < ans.length) {
            ans[index++] = KthLeastNumber;
        }
        return ans;
    }
    
    // 找到数组前k小的数字，不要求顺序
    public int[] getLeastNumbers(int[] arr, int k) {
        return smallestK(arr, k);
    }
    
    // 找到第k大的数 -> 找到第len-k+1小的数字
    public int findKthLargest(int[] arr, int k) {
        return findKthLeast(arr, arr.length - k + 1);
    }
    
    // 找到第k小的数字
    public int findKthLeast(int[] arr, int k) {
        // return quickSortBybfprtByRecursion(arr, 0, arr.length - 1, k - 1);
        // return quickSortBybfprtByIteration(arr, 0, arr.length - 1, k - 1);
        return findKthLeastByMaxHead(arr, k);
    }
    
    private int findKthLeastByMaxHead(int[] arr, int k) {
        PriorityQueue<Integer> maxHead = new PriorityQueue<>((o1, o2) -> o2 - o1);
        // 构建出大小和数组相同的一个大根堆
        for (int num : arr) {
            maxHead.offer(num);
        }
        // 弹出大根堆的元素直到只剩下k个
        while (maxHead.size() > k) {
            maxHead.poll();
        }
        // 此时这个大根堆的堆顶一定是第k小的数
        return maxHead.peek();
    }
    
    private int quickSortBybfprtByRecursion(int[] arr, int left, int right, int index) {
        if (left == right) {
            return arr[left];
        }
        int pivot = medianOfMedians(arr, left, right);
        // int pivot = arr[left + (int) (Math.random() * (right - left + 1))];
        int[] range = partition(arr, left, right, pivot);
        if (range[0] <= index && index <= range[1]) {
            return arr[index];
        } else if (index < range[0]) {
            return quickSortBybfprtByRecursion(arr, left, range[0] - 1, index);
        } else {
            return quickSortBybfprtByRecursion(arr, range[1] + 1, right, index);
        }
    }
    
    // 基于bfprt改进的快速排序迭代版
    private int quickSortBybfprtByIteration(int[] arr, int left, int right, int index) {
        int pivot;
        int[] range;
        while (left < right) {
            // bfprt的核心在于怎么选基准值
            pivot = medianOfMedians(arr, left, right);
            // pivot= arr[left + (int) (Math.random() * (right - left + 1))];
            range = partition(arr, left, right, pivot);
            if (range[0] <= index && index <= range[1]) {
                return pivot;
            } else if (index < range[0]) {
                right = range[0] - 1;
            } else {
                left = range[1] + 1;
            }
        }
        return arr[left];
    }
    
    // 获取中位数数组的中位数作为快排的基准值
    public int medianOfMedians(int[] arr, int left, int right) {
        // 需要考虑的长度
        int length = right - left + 1;
        // 是否能完整的切成每份长度为5
        int offset = length % 5 == 0 ? 0 : 1;
        // 每一份的中位数组成的数组
        int[] medians = new int[length / 5 + offset];
        for (int i = 0; i < medians.length; ++i) {
            // L代表每一份在arr中的开头位置
            int L = left + i * 5;
            // 有可能L+4会越界
            medians[i] = getMedian(arr, L, Math.min(right, L + 4));
        }
        // 让bfprt帮我找到我的中位数，这个中位数数组可能很长
        // 可以混用
        // return quickSortBybfprtByRecursion(medians, 0, medians.length - 1, medians.length / 2);
        return quickSortBybfprtByIteration(medians, 0, medians.length - 1, medians.length / 2);
    }
    
    // 利用插入排序找到一个数组的中位数
    private int getMedian(int[] arr, int left, int right) {
        insertionSort(arr, left, right);
        return arr[(left + right) / 2];
    }
    
    // 小范围的数组排序选常数时间最小的插入排序
    private void insertionSort(int[] arr, int left, int right) {
        for (int i = left + 1; i <= right; ++i) {
            for (int j = i - 1; j >= left && arr[j] > arr[j + 1]; --j) {
                swap(arr, j, j + 1);
            }
        }
    }
    
    // 荷兰国旗问题
    public int[] partition(int[] arr, int left, int right, int pivot) {
        // 小于区的右边界
        int lessAreaRight = left - 1;
        // 大于区的左边界
        // 没有交换到末尾，即最后一个区域不是等于区数字，就从right+1开始
        int moreAreaLeft = right + 1;
        // 迭代指针
        int index = left;
        while (index < moreAreaLeft) {
            if (arr[index] < pivot) {
                // 小于就交换位置且小于区右括，继续遍历
                swap(arr, ++lessAreaRight, index++);
            } else if (arr[index] > pivot) {
                // 大于就交换位置且大于区左括，指针停止
                swap(arr, --moreAreaLeft, index);
            } else {
                // 等于就不交换，遍历下一个
                index++;
            }
        }
        return new int[]{lessAreaRight + 1, moreAreaLeft - 1};
    }
    
    // 交换数组中的两个数
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    
}

// 系统堆实现
class KthLargest {
    
    private PriorityQueue<Integer> heap;
    private int k;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.heap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }
    
    // 先进堆，再把堆调整成k大小，返回堆顶
    public int add(int val) {
        heap.offer(val);
        if (heap.size() > k) {
            heap.poll();
        }
        return heap.peek();
    }
    
}

// 手写堆
class KthLargest {
    
    private final int[] heap;
    private int heapSize;
    
    public KthLargest(int k, int[] nums) {
        // 核心代码，堆的大小由k与nums的大小共同确定
        this.heapSize = Math.max(k, nums.length);
        // 数组大小由题目给定
        this.heap = new int[10001];
        // 拷贝
        System.arraycopy(nums, 0, heap, 0, nums.length);
        // 默认值改为最小值
        for (int i = nums.length; i < 10001; ++i) {
            heap[i] = -10001;
        }
        // 自底向上建堆
        for (int i = nums.length - 1; i >= 0; --i) {
            heapify(heap, i, heapSize);
        }
        // 弹出堆元素直到大小不大于k
        while (heapSize > k) {
            swap(heap, 0, --heapSize);
            heapify(heap, 0, heapSize);
        }
    }
    
    public int add(int val) {
        // 堆为空就加
        if (heapSize == 0) {
            heap[0] = val;
            ++heapSize;
        }
        // 大于第k大就直接替换然后heapify
        // 不然就直接返回，因为小于不需要进堆
        if (val > heap[0]) {
            heap[0] = val;
            heapify(heap, 0, heapSize);
        }
        return heap[0];
    }
    
    private void heapify(int[] heap, int index, int heapSize) {
        int left = index * 2 + 1;
        while (left < heapSize) {
            int least = left + 1 < heapSize && heap[left + 1] < heap[left] ? left + 1 : left;
            least = heap[least] < heap[index] ? least : index;
            if (least == index) {
                break;
            } else {
                swap(heap, least, index);
                index = least;
                left = index * 2 + 1;
            }
        }
    }
    
    // 交换数组中的两个数
    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    
}
```

---

> ***last change: 2022/11/4***

---
