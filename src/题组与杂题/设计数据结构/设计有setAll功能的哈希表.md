# 设计有setAll功能的哈希表

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第9章 其他题目 设计有setAll功能的哈希表

## [设计有setAll功能的哈希表](https://www.nowcoder.com/practice/7c4559f138e74ceb9ba57d76fd169967)

> - ***Question***
>   - 设计一个具有 `put, get, setAll` 功能的哈希表，要求所有方法的时间复杂度都是 `O(1)` 。
>   - `put` 和 `get` 的功能和原生哈希表一样。
>   - `setAll` 指把哈希表中所有 `key` 的 `value` 都改成指定的值。
>   - ***输入描述***
>     - 第一行一个整数 `N` 表示操作数。
>     - 接下来 `N` 行，每行第一个数字 `opt` 代表操作类型：
>       - 若 `opt=1` ，接下来有两个整数 `x, y` 表示设置 `key = x` 对应的 `value = y` 。
>       - 若 `opt=2` ，接下来一个整数 `x` ，表示查询 `key = x` 对应的 `value` ，若 `key = x` 不存在输出 `-1` 。
>       - 若 `opt=3` ，接下来一个整数 `x` ，表示把加入过的所有的 `key` 对应的 `value` 都设置为 `x` 。
>   - ***输出描述***
>     - 对于每个操作 `2` ，输出一个整数表示答案。

---

## *Java*

> - ***时间戳***

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.HashMap;

// 牛客版本
public class Main {

    public static HashMap<Integer, int[]> map = new HashMap<>();
    public static int setAllValue;
    public static int setAllTime;
    public static int cnt;

    public static void put(int k, int v) {
        if (map.containsKey(k)) {
            int[] value = map.get(k);
            value[0] = v;
            value[1] = cnt++;
        } else {
            map.put(k, new int[]{v, cnt++});
        }
    }

    public static void setAll(int v) {
        setAllValue = v;
        setAllTime = cnt++;
    }

    public static int get(int k) {
        if (!map.containsKey(k)) {
            return -1;
        }
        int[] value = map.get(k);
        if (value[1] > setAllTime) {
            return value[0];
        } else {
            return setAllValue;
        }
    }

    public static int n, op, a, b;

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        while (in.nextToken() != StreamTokenizer.TT_EOF) {
            map.clear();
            setAllValue = 0;
            setAllTime = -1;
            cnt = 0;
            n = (int) in.nval;
            for (int i = 0; i < n; i++) {
                in.nextToken();
                op = (int) in.nval;
                if (op == 1) {
                    in.nextToken();
                    a = (int) in.nval;
                    in.nextToken();
                    b = (int) in.nval;
                    put(a, b);
                } else if (op == 2) {
                    in.nextToken();
                    a = (int) in.nval;
                    out.println(get(a));
                } else {
                    in.nextToken();
                    a = (int) in.nval;
                    setAll(a);
                }
            }
        }
        out.flush();
        out.close();
        br.close();
    }

}

// 泛型版本
class MyHashMap<K, V> {
    
    private static class MyValue<V> {
        
        // 将value包装成value+更新成这个value时的时间戳
        public V value;
        public long time;
        
        public MyValue(V value, long time) {
            this.value = value;
            this.time = time;
        }
        
    }
    
    private HashMap<K, MyValue<V>> map;
    // 这个类的操作是单线程串行的，每个setAll或put操作占用一个时间戳
    private long time;
    // 存储setAll操作
    private MyValue<V> setAllValue;
    
    public MyHashMap() {
        map = new HashMap<>();
        time = 0;
        setAllValue = new MyValue<>(null, -1);
    }
    
    public void put(K key, V value) {
        // 正常加入就行
        map.put(key, new MyValue<>(value, time));
        // 时间戳后移
        time++;
    }
    
    public void setAll(V value) {
        // 先记录，不改哈希表
        setAllValue = new MyValue<V>(value, time);
        // 时间戳后移
        time++;
    }
    
    public V get(K key) {
        if (!map.containsKey(key)) {
            // 判空
            return null;
        }
        // 如果原生value更新时的时间戳大于setAll的时间戳，那就用原生，不然用setAll的
        return map.get(key).time > setAllValue.time ? map.get(key).value : setAllValue.value;
    }
    
}
```
