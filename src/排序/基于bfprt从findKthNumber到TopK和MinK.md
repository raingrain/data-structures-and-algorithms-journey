# 基于bfprt从findKthNumber到TopK和MinK

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

## [剑指 Offer II 076. 数组中的第 k 大的数字](https://leetcode.cn/problems/xx4gT2/)

> - ***Question 1***
>   - 给定整数数组 `nums` 和整数 `k` ，请返回数组中第 `k` 个最大的元素。
>   - 请注意，你需要找的是数组排序后的第 `k` 个最大的元素，而不是第 `k` 个不同的元素。
>   - 你必须设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
>   - ***tips:***
>     - `1 <= k <= nums.length <= 10^5`
>     - `-10^4 <= nums[i] <= 10^4`

## [703. 数据流中的第 K 大元素](https://leetcode.cn/problems/kth-largest-element-in-a-stream/)

## [剑指 Offer II 059. 数据流的第 K 大数值](https://leetcode.cn/problems/jBjn9C/)

> - ***Question 2***
>   - 设计一个找到数据流中第 `k` 大元素的类。注意是排序后的第 `k` 大元素，不是第 `k` 个不同的元素。
>   - 请实现 `KthLargest` 类：
>     - `KthLargest(int k, int[] nums)` 使用整数 `k` 和整数流 `nums` 初始化对象。
>     - `int add(int val)` 将 `val` 插入数据流 `nums` 后，返回当前数据流中第 `k` 大的元素。
>   - ***tips:***
>     - `1 <= k <= 10^4`
>     - `0 <= nums.length <= 10^4`
>     - `-10^4 <= nums[i] <= 10^4`
>     - `-10^4 <= val <= 10^4`
>     - 最多调用 `add` 方法 `10^4` 次
>     - 题目数据保证，在查找第 `k` 大元素时，数组中至少有 `k` 个元素

## [面试题 17.14. 最小K个数](https://leetcode.cn/problems/smallest-k-lcci/)

## [剑指 Offer 40. 最小的k个数](https://leetcode.cn/problems/zui-xiao-de-kge-shu-lcof/)

> - ***Question 3***
>   - 找出数组中最小的 `k` 个数。以任意顺序返回这 `k` 个数均可。
>   - ***tips:***
>     - `0 <= k <= arr.length <= 10000`
>     - `0 <= arr[i] <= 10000`

## [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

## [LCR 060. 前 K 个高频元素](https://leetcode.cn/problems/g5c51o/)

> - ***Question 4***
>   - 给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按任意顺序返回答案。
>   - ***tips:***
>     - `1 <= nums.length <= 10^5`
>     - `k` 的取值范围是 `[1, 数组中不相同的元素的个数]`
>     - 题目数据保证答案唯一，换句话说，数组中前 `k` 个高频元素的集合是唯一的

## [692. 前K个高频单词](https://leetcode.cn/problems/top-k-frequent-words/)

> - ***Question 5***
>   - 给定一个单词列表 `words` 和一个整数 `k` ，返回前 `k` 个出现次数最多的单词。
>   - 返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字典顺序排序。
>   - ***tips:***
>     - `1 <= words.length <= 500`
>     - `1 <= words[i] <= 10`
>     - `words[i]` 由小写英文字母组成
>     - `k` 的取值范围是 `[1, 不同 words[i] 的数量]`

## [414. 第三大的数](https://leetcode.cn/problems/third-maximum-number/)

> - ***Question 6***
>   - 给你一个非空数组，返回此数组中第三大的数。如果不存在，则返回数组中最大的数。
>   - ***tips:***
>     - `1 <= nums.length <= 10^4`
>     - `-2^31 <= nums[i] <= 2^31 - 1`

## Java

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

// 系统堆实现找到数据流中第k大的元素
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

// 手写堆实现找到数据流中第k大的元素
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

> - ***Question 4: 小根堆***
>   - 首先遍历整个数组，并使用哈希表记录每个数字出现的次数，并形成一个出现次数数组。找出原数组的前 `k` 个高频元素，就相当于找出出现次数数组的前 `k` 大的值。
>   - 在这里，我们可以利用堆的思想，建立一个小顶堆，然后遍历出现次数数组：
>     - 如果堆的元素个数小于 `k` ，就可以直接插入堆中。
>     - 如果堆的元素个数等于 `k` ，则检查堆顶与当前出现次数的大小。如果堆顶更大，说明至少有 `k` 个数字的出现次数比当前值大，故舍弃当前值；否则，就弹出堆顶，并将当前值插入堆中。
>   - 遍历完成后，堆中的元素就代表了出现次数数组中前 k 大的值。
>   - 时间复杂度 `O(Nlogk)` ，空间复杂度为哈希表大小（比堆大），为 `O(N)` ， `N` 为数组长度， `k` 为限定。

```java
class Solution {
    
    public int[] topKFrequent(int[] nums, int k) {
        // 词频统计
        HashMap<Integer, Integer> occurrences = new HashMap<>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }
        // 小根堆
        // int[] 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(m -> m[1]));
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (minHeap.size() == k) {
                // 比顶部元素大
                if (minHeap.peek()[1] < count) {
                    minHeap.poll();
                    minHeap.offer(new int[]{num, count});
                }
            } else {
                // 堆没满
                minHeap.offer(new int[]{num, count});
            }
        }
        // 收集答案
        int[] ret = new int[k];
        for (int i = 0; i < k; ++i) {
            ret[i] = minHeap.poll()[0];
        }
        return ret;
    }
    
}
```

> - ***Question 5: 哈希表 + 排序***
>   - 我们可以预处理出每一个单词出现的频率，然后依据每个单词出现的频率降序排序，最后返回前 `k` 个字符串即可。
>   - 具体地，我们利用哈希表记录每一个字符串出现的频率，然后将哈希表中所有字符串进行排序，排序时，如果两个字符串出现频率相同，那么我们让两字符串中字典序较小的排在前面，否则我们让出现频率较高的排在前面。最后我们只需要保留序列中的前 `k` 个字符串即可。

```java
import java.util.*;

class Solution {

    public List<String> topKFrequent(String[] words, int k) {
        HashMap<String, Integer> cnt = new HashMap<>();
        for (String word : words) {
            cnt.put(word, cnt.getOrDefault(word, 0) + 1);
        }
        List<String> rec = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : cnt.entrySet()) {
            rec.add(entry.getKey());
        }
        rec.sort((word1, word2) -> Objects.equals(cnt.get(word1), cnt.get(word2)) ? word1.compareTo(word2) : cnt.get(word2) - cnt.get(word1));
        return rec.subList(0, k);
    }

}
```

> - ***Question 5: 小跟堆***
>   - 对于前 `k` 大或前 `k` 小这类问题，有一个通用的解法：优先队列。优先队列可以在 `O(log⁡ n)` 的时间内完成插入或删除元素的操作（其中 `n` 为优先队列的大小），并可以 `O(1)` 地查询优先队列顶端元素。
>   - 在本题中，我们可以创建一个小根优先队列（顾名思义，就是优先队列顶端元素是最小元素的优先队列）。我们将每一个字符串插入到优先队列中，如果优先队列的大小超过了 `k` ，那么我们就将优先队列顶端元素弹出。这样最终优先队列中剩下的 `k` 个元素就是前 `k` 个出现次数最多的单词。

```java
import java.util.*;

class Solution {

    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> cnt = new HashMap<>();
        for (String word : words) {
            cnt.put(word, cnt.getOrDefault(word, 0) + 1);
        }
        PriorityQueue<Map.Entry<String, Integer>> pq = new PriorityQueue<>((entry1, entry2) -> entry1.getValue() == entry2.getValue() ? entry2.getKey().compareTo(entry1.getKey()) : entry1.getValue() - entry2.getValue());
        for (Map.Entry<String, Integer> entry : cnt.entrySet()) {
            pq.offer(entry);
            if (pq.size() > k) {
                pq.poll();
            }
        }
        List<String> ret = new ArrayList<>();
        while (!pq.isEmpty()) {
            ret.add(pq.poll().getKey());
        }
        Collections.reverse(ret);
        return ret;
    }

}
```

> - ***Question 6: 有序表***
>   - 我们可以遍历数组，同时用一个有序集合来维护数组中前三大的数。具体做法是每遍历一个数，就将其插入有序集合，若有序集合的大小超过 `3` ，就删除集合中的最小元素。这样可以保证有序集合的大小至多为 `3` ，且遍历结束后，若有序集合的大小为 `3` ，其最小值就是数组中第三大的数；若有序集合的大小不足 `3` ，那么就返回有序集合中的最大值。

```java
class Solution {

    public int thirdMax(int[] nums) {
        TreeSet<Integer> s = new TreeSet<>();
        for (int num : nums) {
            s.add(num);
            if (s.size() > 3) {
                s.remove(s.first());
            }
        }
        return s.size() == 3 ? s.first() : s.last();
    }

}
```
