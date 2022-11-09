# [剑指 Offer 38. 字符串的排列](https://leetcode.cn/problems/zi-fu-chuan-de-pai-lie-lcof/)

# [面试题 08.07. 无重复字符串的排列组合](https://leetcode.cn/problems/permutation-i-lcci/)

# [面试题 08.08. 有重复字符串的排列组合](https://leetcode.cn/problems/permutation-ii-lcci/)

### 解题思路
1. 暴力递归就直接准备一个保存未选过的字符的数组，然后每次递归从中选一个，同时往path中加入选中的字符，然后进下一层递归，要求没有重复值在主函数内用集合处理一下就可以了
2. 剪枝的话是准备一个char数组，然后由于是全排列，每一个位置上的字符只能与自己及之后的字符进行交换，同时利用一个剪枝数组，遇到相同值就不交换了，交换就会有重复值，最后用了和暴力递归一样的set处理，但似乎不用，不管了
3. 记得清理现场后要恢复现场，即每次都要改变rest或者char数组的时候

### 问题拓展
1. 如果求一个字符串的所有子序列那应该怎么做呢，所有子序列中不要有重复值呢？
2. 求所有子序列的话我们准备一个数组和一个path，每层递归遍历数组中的一个位置，同时会调用两层子递归，区别在于选不选当前字符，传下去的path也会考虑加不加，最后遍历完数组后就往结果中加入path，用一个递增的index判断是否遍历完数组

### 代码

```java
// java递归
class Solution {
    public String[] permutation(String S) {
        // 暴力递归
        // HashSet<String> ans = permutationByRecursion1(S);
        // 剪枝递归
        HashSet<String> ans = permutationByRecursion2(S);
        String[] str = new String[ans.size()];
        int i = 0;
        for (String s : ans) {
            str[i++] = s;
        }
        return str;
    }

    public HashSet<String> permutationByRecursion2(String S) {
        if (S == null || S.length() == 0) {
            return null;
        } else {
            List<String> ans = new ArrayList<>();
            char[] str = S.toCharArray();
            recursion2(str, 0, ans);
            HashSet<String> set = new HashSet<>();
            for (int i = 0; i < ans.size(); i++) {
                set.add(ans.get(i));
            }
            return set;
        }
    }

    // 思路是准备一个char数组，第一个字符可以和所有位置上的字符交换，第一个字符确定以后，第二个字符只能和它以及它后面的字符做交换
    public void recursion2(char[] str, int index, List<String> ans) {
        if (index == str.length) {
            ans.add(String.valueOf(str));
        } else {
            // 剪枝
            boolean[] visited = new boolean[256];
            for (int i = index; i < str.length; i++) {
                // 如果当前位置i和你正在处理的位置index是同一个字符，那就不用处理接下来的分支了
                // 每个循环的第一次都会和自己交换，此时visited中对应位置已经变成了true
                if (!visited[str[i]]) {
                    visited[str[i]] = true;
                    swap(str, index, i);
                    recursion2(str, index + 1, ans);
                    swap(str, index, i);
                }
            }
        }
    }

    public void swap(char[] str, int i, int j) {
        char temp = str[i];
        str[i] = str[j];
        str[j] = temp;
    }

    // 坏版本
    public HashSet<String> permutationByRecursion1(String S) {
        List<String> ans = new ArrayList<>();
        if (S == null || S.length() == 0) {
            return null;
        } else {
            char[] str = S.toCharArray();
            ArrayList<Character> rest = new ArrayList<>();
            for (char temp : str) {
                rest.add(temp);
            }
            String path = "";
            recursion1(rest, path, ans);
            HashSet<String> set = new HashSet<>();
            for (int i = 0; i < ans.size(); i++) {
                set.add(ans.get(i));
            }
            return set;
        }
    }

    // 暴力递归，rest存储剩余的字符，path存储路径
    // 每一次循环都要当前rest数组中的一个字符，并在rest中清除它，把新的rest传给下一级递归，同时path中加上清除的字符，代表你选中了它
    // 但记得回来的时候把清除的字符加回去，因为你选取下一个字符的时候这个字符是要作为rest中的一员传进下一级递归的
    public void recursion1(ArrayList<Character> rest, String path, List<String> ans) {
        // 没有剩余的字符了，path就是答案
        if (rest.isEmpty()) {
            ans.add(path);
        } else {
            int len = rest.size();
            for (int i = 0; i < len; i++) {
                char cur = rest.get(i);
                rest.remove(i);
                recursion1(rest, path + cur, ans);
                rest.add(i, cur);
            }
        }
    }

    // 生成所有子序列，也就是子集
    public List<String> subs(String s) {
        char[] str = s.toCharArray();
        String path = "";
        List<String> ans = new ArrayList<>();
        process1(str, 0, ans, path);
        return ans;
    }

    // str代表数据，index代表挑选到的字符，暗收是结果，path存储当前路径
    public void process1(char[] str, int index, List<String> ans, String path) {
        // str遍历完，一条路径就是一个答案
        if (index == str.length) {
            ans.add(path);
            return;
        }
        // 没有要index位置的字符， path就不需要加上当前位置字符了
        process1(str, index + 1, ans, path);
        // 要了index位置的字符
        process1(str, index + 1, ans, path + String.valueOf(str[index]));
    }

    // 生成所有子序列，也就是子集，但不能有重复的
    public static List<String> subsNoRepeat(String s) {
        char[] str = s.toCharArray();
        String path = "";
        // 用集合就行了
        HashSet<String> set = new HashSet<>();
        process2(str, 0, set, path);
        List<String> ans = new ArrayList<>();
        for (String cur : set) {
            ans.add(cur);
        }
        return ans;
    }

    public static void process2(char[] str, int index, HashSet<String> set, String path) {
        if (index == str.length) {
            set.add(path);
            return;
        }
        process2(str, index + 1, set, path);
        process2(str, index + 1, set, path + String.valueOf(str[index]));
    }
}
```
