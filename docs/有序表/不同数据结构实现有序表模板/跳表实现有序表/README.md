# 跳表实现有序表

![image](./images/跳表.png)

## [P3369 【模板】普通平衡树](https://www.luogu.com.cn/problem/P3369)

> - **Question**
>   - 您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：
>     1. 插入一个数 `x` 。
>     2. 删除一个数 `x` （若有多个相同的数，应只删除一个）。
>     3. 定义排名为比当前数小的数的个数 `+ 1` 。查询 `x` 的排名。
>     4. 查询数据结构中排名为 `x` 的数。
>     5. 求 `x` 的前驱（前驱定义为小于 `x` ，且最大的数）。
>     6. 求 `x` 的后继（后继定义为大于 `x` ，且最小的数）。
>   - 对于操作 `3, 5, 6` ，不保证当前数据结构中存在数 `x` 。
>   - **输入描述**
>     - 第一行为 `n` ，表示操作的个数,下面 `n` 行每行有两个数 `opt` 和 `x` ， `opt` 表示操作的序号（ `1 <= opt <= 6` ）。
>   - **输出描述**
>     - 对于操作 `3, 4, 5, 6` 每行输出一个数，表示对应答案。
>   - **Tips**
>     - `1 <=n <= 10^5`
>     - `|x| <= 10^7`

## Java

> - **节点类实现SkipList**
>   - 与传统的平衡二叉搜索树相比，跳表通过百分之 `50` 上升百分之 `50` 在这一层的概率使得同样的一组序列形成的跳表的性质互不相同，即占据高度的随机性构造了形状的随机性，将输入的不完美（树形结构是依据用户的输入决定的，还要自己调平衡）抹除，在概率上实现了平衡。
>   - 从最高层向右下以阶梯式渐进查找的写法使得跳表查找起来非常快，在高层的每一次移动可能都会划过低层的一大片数字（相当于剪枝）（因为每一层都有自己的数学期望即 `0.5 ^ height` ，来到高层的数字注定少于低层的数字，最好情况每次剪一半的枝，数据越多层数越高），其时间复杂度也是 `O(logN)` 水平。
>   - 增删节点都是从左上找到右下的写法，如果从第 `0` 层开始删除那就退化成为链表了，具体做法是我们每次从最高层的 `head` 开始找，往右到 `key` 值最接近要操作的节点，看看它的写一个节点，是要操作的节点就操作，不然就直接从这个节点进入到下一层（而不是从 `head` 开始下一层，这就是上述所说的高效查找），继续往右找，重复进行这一过程。

```java
// 有序表接口
interface OrderedList<K extends Comparable<K>, V> {
    
    // 有序表的元素数量
    public int size();
    
    // 有序表是否为空
    public boolean isEmpty();
    
    // 有序表中有没有这个key
    public boolean containsKey(K key);
    
    // 往有序表中加入指定key
    public void put(K key, V value);
    
    // 在有序表中移除指定key
    public void remove(K key);
    
    // 返回指定key的value
    public V get(K key);
    
    // 找到有序表中索引为index的键
    public K getIndexKey(int index);
    
    // 找到有序表中索引为index的键对应的值
    public V getIndexValue(int index);
    
    // 返回有序表中的最小key
    public K minimumKey();
    
    // 返回有序表中的最大key
    public K maximumKey();
    
    // 返回有序表中小于或等于给定键的最大键，如果没有这样的键，则 null
    public K floorKey(K key);
    
    // 返回有序表中大于或等于给定键的最小键，如果没有这样的键，则 null
    public K ceilingKey(K key);
    
}

class SLNode<K extends Comparable<K>, V> {
    
    public K key;
    public V value;
    public ArrayList<SLNode<K, V>> nexts;
    
    public SLNode(K key, V value) {
        this.key = key;
        this.value = value;
        this.nexts = new ArrayList<>();
    }
    
    // 将当前节点的key与传入的key作比较，判断是否小于传入key，是返回true反之false
    public boolean isKeyLess(K otherKey) {
        // 首先otherKey不能为空
        // 其次key可以为空也可以小于它
        return otherKey != null && (key == null || key.compareTo(otherKey) < 0);
    }
    
    // 将当前节点的key与传入的key作比较，判断是否等于传入key，是返回true反之false
    public boolean isKeyEqual(K otherKey) {
        // 两者都为空
        // 或者都不为空且值相等
        return (key == null && otherKey == null) || (key != null && otherKey != null && key.compareTo(otherKey) == 0);
    }
    
}

class SkipList<K extends Comparable<K>, V> implements OrderedList<K, V> {
    
    // 加入数据越多，层数就越高
    private static final double PROBABILITY = 0.5; // < 0.5 继续做，>=0.5 停
    private SLNode<K, V> head;
    private int size;
    private int maxLevel;
    
    public SkipList() {
        head = new SLNode<>(null, null);
        head.nexts.add(null);
        size = 0;
        maxLevel = 0;
    }
    
    // 在第level层中，从cur节点开始往后遍历，找到key值小于给定key值的节点中key值最大的节点返回
    private SLNode<K, V> mostRightLessNodeInLevel(K key, SLNode<K, V> cur, int level) {
        // 拿到这个节点在这一层上的下一个节点
        SLNode<K, V> next = cur.nexts.get(level);
        // 当第一次发现一个节点的key大于给定key时返回其上一个节点
        // 当下一个不为空且下一个小于时，cur前进
        // next有可能遇到空，也有可能找到相同的，也有可能遇到比key大的停
        while (next != null && next.isKeyLess(key)) {
            cur = next;
            next = cur.nexts.get(level);
        }
        return cur;
    }
    
    // 在跳表中，从cur节点开始往后遍历，找到key值小于给定key值的节点中key值最大的节点返回
    private SLNode<K, V> mostRightLessNodeInSkipList(K key) {
        if (key == null) {
            return null;
        }
        // 自左上向右下阶梯式查找
        int level = maxLevel;
        SLNode<K, V> cur = head;
        while (level >= 0) {
            cur = mostRightLessNodeInLevel(key, cur, level--);
        }
        return cur;
    }
    
    
    @Override
    public int size() {
        return size;
    }
    
    @Override
    public boolean isEmpty() {
        return size == 0;
    }
    
    @Override
    public boolean containsKey(K key) {
        if (key == null) {
            return false;
        }
        // 找到整个表中小于key的最大节点less
        SLNode<K, V> less = mostRightLessNodeInSkipList(key);
        SLNode<K, V> next = less.nexts.get(0);
        return next != null && next.isKeyEqual(key);
    }
    
    @Override
    public void put(K key, V value) {
        if (key == null) {
            return;
        }
        // 找到整个表中小于key的最大节点less
        SLNode<K, V> less = mostRightLessNodeInSkipList(key);
        SLNode<K, V> next = less.nexts.get(0);
        if (next != null && next.isKeyEqual(key)) {
            // 看看less的下一个节点是不是key
            // 是就直接改值
            next.value = value;
        } else {
            // 不是就加进去
            ++size;
            int newNodelevel = 0;
            // 摇骰子，每次以0.5的概率决定进不进入更高一层
            while (Math.random() < PROBABILITY) {
                ++newNodelevel;
            }
            // 如果新节点摇到的高度大于当前最高高度，在头节点处把高度往上升
            while (newNodelevel > maxLevel) {
                head.nexts.add(null);
                ++maxLevel;
            }
            // 建好新节点
            SLNode<K, V> newNode = new SLNode<>(key, value);
            // 将新节点的nexts数组全挂为空先，你的层数是多少你就挂多少
            for (int i = 0; i <= newNodelevel; ++i) {
                newNode.nexts.add(null);
            }
            int level = maxLevel;
            SLNode<K, V> pre = head;
            while (level >= 0) {
                pre = mostRightLessNodeInLevel(key, pre, level);
                // 有可能我新进来的节点的高度小于当前表的最大高度
                // 当遍历的高度降低到与newSLNodeLevel齐平时才开始往跳表中加数据
                if (level <= newNodelevel) {
                    // 我在这一层接管你在这一层的下一个
                    newNode.nexts.set(level, pre.nexts.get(level));
                    // 在这一层你接管我
                    pre.nexts.set(level, newNode);
                }
                // 往下面层遍历
                --level;
            }
        }
    }
    
    @Override
    public void remove(K key) {
        if (containsKey(key)) {
            --size;
            int level = maxLevel;
            SLNode<K, V> pre = head;
            while (level >= 0) {
                pre = mostRightLessNodeInLevel(key, pre, level);
                SLNode<K, V> next = pre.nexts.get(level);
                if (next != null && next.isKeyEqual(key)) {
                    // 下一个就是你，把你从链表中移除
                    pre.nexts.set(level, next.nexts.get(level));
                }
                // 在level层只有默认节点head，为了省空间删除链表
                if (level != 0 && pre == head && pre.nexts.get(level) == null) {
                    head.nexts.remove(level);
                    --maxLevel;
                }
                --level;
            }
        }
    }
    
    @Override
    public V get(K key) {
        if (key == null) {
            return null;
        }
        SLNode<K, V> less = mostRightLessNodeInSkipList(key);
        SLNode<K, V> next = less.nexts.get(0);
        return next != null && next.isKeyEqual(key) ? next.value : null;
    }
    
    @Override
    public K minimumKey() {
        return head.nexts.get(0) == null ? null : head.nexts.get(0).key;
    }
    
    @Override
    public K maximumKey() {
        int level = maxLevel;
        SLNode<K, V> cur = head;
        while (level >= 0) {
            SLNode<K, V> next = cur.nexts.get(level);
            while (next != null) {
                cur = next;
                next = next.nexts.get(level);
            }
            --level;
        }
        return cur.key;
    }
    
    @Override
    public K floorKey(K key) {
        SLNode<K, V> less = mostRightLessNodeInSkipList(key);
        SLNode<K, V> next = less.nexts.get(0);
        // 看看有没有下一个节点，且下一个节点等于key吗
        // 下一个节点只有空、大于key和等于key三种情况，不会小于key，因为有mostRightLessNodeInSkipList()函数
        return next != null && next.isKeyEqual(key) ? next.key : less.key;
    }
    
    @Override
    public K ceilingKey(K key) {
        if (key == null) {
            return null;
        }
        SLNode<K, V> less = mostRightLessNodeInSkipList(key);
        SLNode<K, V> next = less.nexts.get(0);
        return next == null ? null : next.key;
    }
    
    // 跳表只能从0层开始有序查找
    // 且是按照key有序查找，而不是加入时的顺序，时间复杂度为O(index)
    @Override
    public K getIndexKey(int index) {
        if (index < 0 || index > size) {
            return null;
        }
        SLNode<K, V> cur = head;
        for (int i = 0; i < index; ++i) {
            cur = cur.nexts.get(0);
        }
        return cur.key;
    }
    
    @Override
    public V getIndexValue(int index) {
        if (index < 0 || index > size) {
            return null;
        }
        SLNode<K, V> cur = head;
        for (int i = 0; i < index; ++i) {
            cur = cur.nexts.get(0);
        }
        return cur.value;
    }
    
}
```

> - **Question**

```java
import java.io.*;
import java.util.Arrays;

public class Main {

    // 跳表最大层的限制
    public static int MAXL = 20;

    public static int MAXN = 100001;

    // 空间使用计数
    public static int cnt;

    // 节点的key
    public static int[] key = new int[MAXN];

    // 节点key的计数
    public static int[] count = new int[MAXN];

    // 节点拥有多少层指针
    public static int[] level = new int[MAXN];

    // 节点每一层指针指向哪个节点
    public static int[][] next = new int[MAXN][MAXL + 1];

    // 节点每一层指针的长度(底层跨过多少数，左开右闭)
    public static int[][] len = new int[MAXN][MAXL + 1];

    // 建立跳表
    public static void build() {
        cnt = 1;
        key[cnt] = Integer.MIN_VALUE;
        level[cnt] = MAXL;
    }

    // 使用多少空间一律清空
    public static void clear() {
        Arrays.fill(key, 1, cnt + 1, 0);
        Arrays.fill(count, 1, cnt + 1, 0);
        Arrays.fill(level, 1, cnt + 1, 0);
        for (int i = 1; i <= cnt; i++) {
            Arrays.fill(next[i], 0);
            Arrays.fill(len[i], 0);
        }
        cnt = 0;
    }

    // 扔骰子决定节点的层数
    public static int random() {
        int ans = 1;
        while (Math.random() < 0.5) {
            ans++;
        }
        return Math.min(ans, MAXL);
    }

    // 当前在i号节点的h层，返回key为num的节点，空间编号是多少
    public static int find(int i, int h, int num) {
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            i = next[i][h];
        }
        if (h == 1) {
            if (next[i][h] != 0 && key[next[i][h]] == num) {
                return next[i][h];
            } else {
                return 0;
            }
        }
        return find(i, h - 1, num);
    }

    // 增加num，重复加入算多个词频
    public static void add(int num) {
        if (find(1, MAXL, num) != 0) {
            addCount(1, MAXL, num);
        } else {
            key[++cnt] = num;
            count[cnt] = 1;
            level[cnt] = random();
            addNode(1, MAXL, cnt);
        }
    }

    // 当前在i号节点的h层，num增加一个词频
    public static void addCount(int i, int h, int num) {
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            i = next[i][h];
        }
        if (h == 1) {
            count[next[i][h]]++;
        } else {
            addCount(i, h - 1, num);
        }
        len[i][h]++;
    }

    // 当前在i号节点的h层，插入空间编号为j的节点
    // 返回值：从i号节点出发，直到把空间编号为j的节点插入，底层总共有多少数字比key[j]小
    // 返回值很重要，因为上游需要这个信息来改动指针的长度信息
    public static int addNode(int i, int h, int j) {
        int rightCnt = 0;
        while (next[i][h] != 0 && key[next[i][h]] < key[j]) {
            rightCnt += len[i][h];
            i = next[i][h];
        }
        if (h == 1) {
            next[j][h] = next[i][h];
            next[i][h] = j;
            len[j][h] = count[next[j][h]];
            len[i][h] = count[next[i][h]];
            return rightCnt;
        } else {
            int downCnt = addNode(i, h - 1, j);
            if (h > level[j]) {
                len[i][h]++;
            } else {
                next[j][h] = next[i][h];
                next[i][h] = j;
                len[j][h] = len[i][h] + 1 - downCnt - count[j];
                len[i][h] = downCnt + count[j];
            }
            return rightCnt + downCnt;
        }
    }

    // 删除x，如果有多个，只删掉一个
    public static void remove(int num) {
        int j = find(1, MAXL, num);
        if (j != 0) {
            if (count[j] > 1) {
                removeCount(1, MAXL, num);
            } else {
                removeNode(1, MAXL, j);
            }
        }
    }

    // 当前在i号节点的h层，num减少一个词频
    public static void removeCount(int i, int h, int num) {
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            i = next[i][h];
        }
        if (h == 1) {
            count[next[i][h]]--;
        } else {
            removeCount(i, h - 1, num);
        }
        len[i][h]--;
    }

    // 当前在i号节点的h层，删除空间编号为j的节点
    public static void removeNode(int i, int h, int j) {
        if (h < 1) {
            return;
        }
        while (next[i][h] != 0 && key[next[i][h]] < key[j]) {
            i = next[i][h];
        }
        if (h > level[j]) {
            len[i][h]--;
        } else {
            next[i][h] = next[j][h];
            len[i][h] += len[j][h] - 1;
        }
        removeNode(i, h - 1, j);
    }

    // 查询num的排名
    public static int rank(int num) {
        return small(1, MAXL, num) + 1;
    }

    // 当前在i号节点的h层，查询有多少个数字比num小
    public static int small(int i, int h, int num) {
        int rightCnt = 0;
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            rightCnt += len[i][h];
            i = next[i][h];
        }
        if (h == 1) {
            return rightCnt;
        } else {
            return rightCnt + small(i, h - 1, num);
        }
    }

    // 查询排名第x的key是什么
    public static int index(int x) {
        return index(1, MAXL, x);
    }

    // 当前在i号节点的h层，查询排名第x的key是什么
    public static int index(int i, int h, int x) {
        int c = 0;
        while (next[i][h] != 0 && c + len[i][h] < x) {
            c += len[i][h];
            i = next[i][h];
        }
        if (h == 1) {
            return key[next[i][h]];
        } else {
            return index(i, h - 1, x - c);
        }
    }

    // 查询num的前驱
    public static int pre(int num) {
        return pre(1, MAXL, num);
    }

    public static int pre(int i, int h, int num) {
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            i = next[i][h];
        }
        if (h == 1) {
            return i == 1 ? Integer.MIN_VALUE : key[i];
        } else {
            return pre(i, h - 1, num);
        }
    }

    // 查询num的后继
    public static int post(int num) {
        return post(1, MAXL, num);
    }

    public static int post(int i, int h, int num) {
        while (next[i][h] != 0 && key[next[i][h]] < num) {
            i = next[i][h];
        }
        if (h == 1) {
            if (next[i][h] == 0) {
                return Integer.MAX_VALUE;
            }
            if (key[next[i][h]] > num) {
                return key[next[i][h]];
            }
            i = next[i][h];
            if (next[i][h] == 0) {
                return Integer.MAX_VALUE;
            } else {
                return key[next[i][h]];
            }
        } else {
            return post(i, h - 1, num);
        }
    }

    public static void main(String[] args) throws IOException {
        build();
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        for (int i = 1, op, x; i <= n; i++) {
            in.nextToken();
            op = (int) in.nval;
            in.nextToken();
            x = (int) in.nval;
            if (op == 1) {
                add(x);
            } else if (op == 2) {
                remove(x);
            } else if (op == 3) {
                out.println(rank(x));
            } else if (op == 4) {
                out.println(index(x));
            } else if (op == 5) {
                out.println(pre(x));
            } else {
                out.println(post(x));
            }
        }
        clear();
        out.flush();
        out.close();
        br.close();
    }

}
```
