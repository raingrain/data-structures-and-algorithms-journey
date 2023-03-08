# 设计有setAll功能的哈希表

## 程序员代码面试指南：IT名企算法与数据结构题目最优解（第二版） 第9章 其他题目 设计有setAll功能的哈希表

> - ***Question***
>   - 设计一个具有 `put, get, setAll` 功能的哈希表，要求所有方法的时间复杂度都是 `O(1)` 。
>   - `put` 和 `get` 的功能和原生哈希表一样。
>   - `setAll` 指把哈希表中所有 `key` 的 `value` 都改成指定的值。

---

## *Java*

> - ***时间戳***

```java
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

---

> ***last change: 2023/3/8***

---
