---
layout:     post
title:      "CopyOnWrite机制"
subtitle:   ""
date:       2016-12-21 00:58:21
author:     "小白"
tags:
    - Java后端
    - 并发编程
---
即写时复制。当我们往一个容器添加元素的时候，不直接添加到容器中，而是先将这个容器复制一份，将元素添加到复制的容器中，再将原容器的引用指向复制的新容器。

这样做的好处是可以并发地读且不用加锁，也是一种读写分离的思想。

试着实现一个CopyOnWriteMap:

```java
import java.util.Collection;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/**
 * Created by Administrator on 2016/12/21.
 */
public class CopyOnWriteMap<K,V> implements Map<K,V>,Cloneable {
    private volatile Map<K,V> internalMap;

    public CopyOnWriteMap(){
        internalMap=new HashMap<K,V>();
    }

    @Override
    public int size() {
        return 0;
    }

    @Override
    public boolean isEmpty() {
        return false;
    }

    @Override
    public boolean containsKey(Object key) {
        return false;
    }

    @Override
    public boolean containsValue(Object value) {
        return false;
    }

    @Override
    public V get(Object key) {
        return internalMap.get(key);
    }

    @Override
    public V put(K key, V value) {
        synchronized (this){
            Map<K,V> newMap=new HashMap<K,V>(internalMap);
            V val=newMap.put(key,value);
            internalMap=newMap;
            return val;
        }
    }

    @Override
    public V remove(Object key) {
        return null;
    }

    @Override
    public void putAll(Map<? extends K, ? extends V> newData) {
        synchronized (this){
            Map<K,V> newMap=new HashMap<>(internalMap);
            newMap.putAll(newData);
            internalMap=newMap;
        }
    }

    @Override
    public void clear() {

    }

    @Override
    public Set<K> keySet() {
        return null;
    }

    @Override
    public Collection<V> values() {
        return null;
    }

    @Override
    public Set<Entry<K, V>> entrySet() {
        return null;
    }
}
```
# 适用场景 #
适用读多写少的并发场景。
需注意：

- 减少扩容开销。根据实际需要，初始化CopyOnWriteMap的大小，避免写时CopyOnWriteMap扩容的开销。
- 使用批量添加。因为每次添加都会对容器进行复制，所以减少复制次数也是必要的。

缺点：

- 内存占用。写时会同时驻扎两个对象的内存，容易造成GC。
- 数据一致性问题。可能导致读到的数据不是最新的，因为写时没有为旧的容器加读锁。
