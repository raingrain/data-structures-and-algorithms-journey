# 将str2插入到str1使得字典序最大

## 左老师课堂讲述

> - ***Question***
>   - 给定两个字符串str1和str2，想把str2整体插入到str1中的某个位置，形成最大的字典序，返回字典序最大的结果。

## Java

> - ***DC3算法生成后缀数组***

```java
class Solution {
    
    // 正式方法 O(N+M) + O(M^2)
    // N : s1长度
    // M : s2长度
    public static String maxCombine(String s1, String s2) {
        if (s1 == null || s1.length() == 0) {
            return s2;
        }
        if (s2 == null || s2.length() == 0) {
            return s1;
        }
        char[] str1 = s1.toCharArray();
        char[] str2 = s2.toCharArray();
        // 找到str1和str2的并集中最大的字符和最小的字符
        int min = str1[0];
        int max = str1[0];
        for (char c : str1) {
            min = Math.min(min, c);
            max = Math.max(max, c);
        }
        for (char c : str2) {
            min = Math.min(min, c);
            max = Math.max(max, c);
        }
        // 打造出规定的all数组，[str1...1...str2]，str1和str2中最小为2
        int[] all = new int[str1.length + str2.length + 1];
        int index = 0;
        for (char c : str1) {
            all[index++] = c - min + 2;
        }
        // 插入一个最小值用于分割str1和str2
        all[index++] = 1;
        for (char c : str2) {
            all[index++] = c - min + 2;
        }
        DC3 dc3 = new DC3(all, max - min + 2);
        // 索引是all的索引，值是其索引开头的后缀数组的排名
        int[] rank = dc3.rank;
        // str2在all中的位置
        int comp = str1.length + 1;
        for (int i = 0; i < str1.length; i++) {
            // 如果str1中有一个后缀字符串的字典序比str2小
            // 那么插入点可以确定在一个范围内
            // 我们要找到最佳插入点
            if (rank[i] < rank[comp]) {
                int best = bestSplit(s1, s2, i);
                // 拼接直接返回
                return s1.substring(0, best) + s2 + s1.substring(best);
            }
        }
        // 如果str1中所有后缀字符串的字典序都比str2大，那么str2直接插在str1后边
        return s1 + s2;
    }
    
    // 给定字符串和后缀串第一次小于的位置，查询插入位置
    public static int bestSplit(String s1, String s2, int first) {
        int end = s1.length();
        // s1[first...]与s2[...]比较，找到使这个后缀串比str2小的那个位置
        // b aaab cd aaac gh
        // s1中插入位置左端点在b和a之间，由于是第二个b影响了aaab < aaac，那么插入位置右端点在a和b之间
        for (int i = first, j = 0; i < s1.length() && j < s2.length(); i++, j++) {
            if (s1.charAt(i) < s2.charAt(j)) {
                end = i;
                break;
            }
        }
        String bestPrefix = s2;
        int bestSplit = first;
        // 我们只比较aaab中插入aaac的情况
        for (int i = first + 1, j = s2.length() - 1; i <= end; i++, j--) {
            String curPrefix = s1.substring(first, i) + s2.substring(0, j);
            if (curPrefix.compareTo(bestPrefix) >= 0) {
                bestPrefix = curPrefix;
                bestSplit = i;
            }
        }
        // 返回插入位置
        return bestSplit;
    }
    
}

class DC3 {
    
    public int[] sa;
    
    public int[] rank;
    
    public int[] height;
    
    // 构造方法的约定:
    // 数组叫nums，如果你是字符串，请转成整型数组nums
    // 数组中，最小值>=1
    // 如果不满足，处理成满足的，也不会影响使用
    // max, nums里面最大值是多少
    public DC3(int[] nums, int max) {
        sa = sa(nums, max);
        rank = rank();
        height = height(nums);
    }
    
    private int[] sa(int[] nums, int max) {
        int n = nums.length;
        int[] arr = new int[n + 3];
        System.arraycopy(nums, 0, arr, 0, n);
        return skew(arr, n, max);
    }
    
    private int[] skew(int[] nums, int n, int K) {
        int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
        int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
        for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
            if (0 != i % 3) {
                s12[j++] = i;
            }
        }
        radixPass(nums, s12, sa12, 2, n02, K);
        radixPass(nums, sa12, s12, 1, n02, K);
        radixPass(nums, s12, sa12, 0, n02, K);
        int name = 0, c0 = -1, c1 = -1, c2 = -1;
        for (int i = 0; i < n02; ++i) {
            if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
                name++;
                c0 = nums[sa12[i]];
                c1 = nums[sa12[i] + 1];
                c2 = nums[sa12[i] + 2];
            }
            if (1 == sa12[i] % 3) {
                s12[sa12[i] / 3] = name;
            } else {
                s12[sa12[i] / 3 + n0] = name;
            }
        }
        if (name < n02) {
            sa12 = skew(s12, n02, name);
            for (int i = 0; i < n02; i++) {
                s12[sa12[i]] = i + 1;
            }
        } else {
            for (int i = 0; i < n02; i++) {
                sa12[s12[i] - 1] = i;
            }
        }
        int[] s0 = new int[n0], sa0 = new int[n0];
        for (int i = 0, j = 0; i < n02; i++) {
            if (sa12[i] < n0) {
                s0[j++] = 3 * sa12[i];
            }
        }
        radixPass(nums, s0, sa0, 0, n0, K);
        int[] sa = new int[n];
        for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
            int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
            int j = sa0[p];
            if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
                    : leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
                sa[k] = i;
                t++;
                if (t == n02) {
                    for (k++; p < n0; p++, k++) {
                        sa[k] = sa0[p];
                    }
                }
            } else {
                sa[k] = j;
                p++;
                if (p == n0) {
                    for (k++; t < n02; t++, k++) {
                        sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
                    }
                }
            }
        }
        return sa;
    }
    
    private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
        int[] cnt = new int[k + 1];
        for (int i = 0; i < n; ++i) {
            cnt[nums[input[i] + offset]]++;
        }
        for (int i = 0, sum = 0; i < cnt.length; ++i) {
            int t = cnt[i];
            cnt[i] = sum;
            sum += t;
        }
        for (int i = 0; i < n; ++i) {
            output[cnt[nums[input[i] + offset]]++] = input[i];
        }
    }
    
    private boolean leq(int a1, int a2, int b1, int b2) {
        return a1 < b1 || (a1 == b1 && a2 <= b2);
    }
    
    private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
        return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
    }
    
    private int[] rank() {
        int n = sa.length;
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            ans[sa[i]] = i;
        }
        return ans;
    }
    
    private int[] height(int[] s) {
        int n = s.length;
        int[] ans = new int[n];
        for (int i = 0, k = 0; i < n; ++i) {
            if (rank[i] != 0) {
                if (k > 0) {
                    --k;
                }
                int j = sa[rank[i] - 1];
                while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
                    ++k;
                }
                ans[rank[i]] = k;
            }
        }
        return ans;
    }
    
}
```
