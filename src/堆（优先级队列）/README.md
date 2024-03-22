# 堆相关代码模板

---

## *Java*

> - ***堆重要属性和方法***
>   - `HashMap<T, Integer> indexMap` 反向索引表
>   - `void heapInsert(int index)` 看看元素可否向上移动
>   - `void heapIFY(int index)` 看看元素可否下沉
>   - `void resign(Node<T> node)` 对一个元素进行修改后调整它在堆中的位置

```java
// 用于对数组升序排序的简单版大根堆
class maxHeapForHeapSort {
    
    private void swap(int[] heap, int i, int j) {
        int temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }
    
    private void heapInsert(int[] heap, int index) {
        // 我比我的父亲大，我就和它交换位置
        // 同时考虑了index = 0时heap[index] == heap[(index - 1) / 2]
        while (heap[index] > heap[(index - 1) / 2]) {
            swap(heap, index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
    }
    
    // 下沉
    private void heapify(int[] heap, int index, int heapSize) {
        int left = index * 2 + 1;
        while (left < heapSize) {
            int largest = left + 1 < heapSize && heap[left + 1] > heap[left] ? left + 1 : left;
            largest = heap[largest] > heap[index] ? largest : index;
            if (largest == index) {
                break;
            } else {
                swap(heap, largest, index);
                index = largest;
                left = index * 2 + 1;
            }
        }
    }
    
    private void heapSort(int[] array) {
        if (array == null || array.length < 2) {
            return;
        }
        // 两种建堆方式选第二种
        // O(N * logN)
        /*for (int i = 0; i < array.length; ++i) {
            heapInsert(array, i);
        }*/
        // O(logN)
        for (int i = array.length - 1; i >= 0; --i) {
            heapify(array, i, array.length);
        }
        int heapSize = array.length;
        swap(array, 0, --heapSize);
        while (heapSize > 0) {
            heapify(array, 0, heapSize);
            swap(array, 0, --heapSize);
        }
    }
    
}

// 使用比较器、泛型、容器的加强堆类
// T必须是非基础类型
class HeapGreater<T> {
    
    // 堆本体
    private final ArrayList<T> heap;
    // 反向索引表，加强堆最重要的东西，可以根据元素获得索引
    private final HashMap<T, Integer> indexMap;
    // 堆大小
    private int heapSize;
    // 比较器
    private final Comparator<? super T> comp;
    
    public HeapGreater(Comparator<T> comp) {
        this.heap = new ArrayList<>();
        this.indexMap = new HashMap<>();
        this.heapSize = 0;
        this.comp = comp;
    }
    
    // 给定索引。交换堆中的两个元素
    private void swap(int i, int j) {
        // 拿到元素
        T o1 = heap.get(i);
        T o2 = heap.get(j);
        // 通过set方法交换
        heap.set(i, o2);
        heap.set(j, o1);
        // 反向索引表中的记录也要交换
        indexMap.put(o1, j);
        indexMap.put(o2, i);
    }
    
    // 插入，向上检查
    private void heapInsert(int index) {
        // 这个比较器的意思是index位置的元素应该放在前面，那我和我的父比较，我要是应该放在前面我就和我的父交换
        while (comp.compare(heap.get(index), heap.get((index - 1) / 2)) < 0) {
            swap(index, (index - 1) / 2);
            index = (index - 1) / 2;
        }
    }
    
    // 下沉，代码基本一样
    private void heapIFY(int index) {
        int left = index * 2 + 1;
        while (left < heapSize) {
            // 同样的，看谁应该在前面，在就交换
            int best = left + 1 < heapSize && comp.compare(heap.get(left + 1), heap.get(left)) < 0 ? left + 1 : left;
            best = comp.compare(heap.get(best), heap.get(index)) < 0 ? best : index;
            if (best == index) {
                break;
            } else {
                swap(best, index);
                index = best;
                left = index * 2 + 1;
            }
        }
    }
    
    // 修改了堆中的某一个元素
    public void resign(T obj) {
        // 两者其实只能中一个
        heapInsert(indexMap.get(obj));
        heapIFY(indexMap.get(obj));
    }
    
    //堆是否为空
    public boolean isEmpty() {
        return heapSize == 0;
    }
    
    // 堆大小
    public int size() {
        return heapSize;
    }
    
    // 堆是否包含这一元素
    public boolean contains(T obj) {
        return indexMap.containsKey(obj);
    }
    
    // 获取堆顶
    public T peek() {
        return heap.get(0);
    }
    
    // 往堆中加入元素
    public void push(T obj) {
        // 先加
        heap.add(obj);
        // 反向索引表也加
        indexMap.put(obj, heapSize);
        // 调整
        heapInsert(heapSize++);
    }
    
    // 弹出并删除堆顶
    public T pop() {
        // 先抓住堆顶
        T ans = heap.get(0);
        // 和最后一个元素交换位置
        swap(0, heapSize - 1);
        // 反向索引表中删除
        indexMap.remove(ans);
        // 堆移除最后一个元素
        heap.remove(--heapSize);
        // 换上来的节点下沉
        heapIFY(0);
        return ans;
    }
    
    // 删除堆中的一个元素
    public void remove(T obj) {
        // 找到最后一个元素作为替换元素
        T replace = heap.get(heapSize - 1);
        // 要删除的位置
        int index = indexMap.get(obj);
        // 反向索引表删除
        indexMap.remove(obj);
        // 堆把最后一个元素删除
        heap.remove(--heapSize);
        // 如果要删除的元素就是最后一个元素就直接结束
        if (replace != obj) {
            // 把index位置的元素设置为替换元素
            heap.set(index, replace);
            // 反向索引表更新
            indexMap.put(replace, index);
            // 针对某个位置发生了改变，insert和IFY都看一下
            resign(replace);
        }
    }
    
    // 返回堆中的所有元素
    public List<T> getAllElements() {
        // 不确定会不会影响内存
        List<T> ans = new ArrayList<>();
        for (T t : heap) {
            ans.add(t);
        }
        return ans;
        // return new ArrayList<>(heap);
    }
    
}
```

---

> ***last change: 2022/10/30***

---
