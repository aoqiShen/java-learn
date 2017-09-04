# First Chapter

HashMap的结构设计

HashMap采取的的是数组加联表的存储形式, 其中数据\(散列桶\)中的每一个元素都是链表![](/assets/import.png)

若某一个元素加入到数据集合中 先判断其key的hash值, 之后会遍历链表中的元素, 加入到末尾

其中列举了HashMap中重要的属性参数

```

static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

// 证明Map的数据能映射到很大的范围
static final int MAXIMUM_CAPACITY = 1 << 30;

// 默认的负载因子 
static final float DEFAULT_LOAD_FACTOR = 0.75f;

transient Entry<K,V>[] table;

// 数据的结构
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
    
    threshold是HashMap的重构阈值 是容量和负载因子的乘积， 若是数据量达到一定范围 会进行扩容
    final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
        int s = m.size();
        if (s > 0) {
            if (table == null) { // pre-size
                float ft = ((float)s / loadFactor) + 1.0F;
                int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                         (int)ft : MAXIMUM_CAPACITY);
                if (t > threshold)
                    threshold = tableSizeFor(t);
            }
            else if (s > threshold)
                resize();
            for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
                K key = e.getKey();
                V value = e.getValue();
                putVal(hash(key), key, value, false, evict);
            }
        }
    }
```





ConcurrentHashMap与 HashMap的区别：

通过CAS保证数据

