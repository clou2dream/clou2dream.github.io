---
layout:     post
title:      foreach 循环与 iterator 迭代器遍历
subtitle:   foreach iterator 实现与比较
date:       2019-06-13
author:     clou2dream
header-img: img/home-bg-art.jpg
keywords_post:  "collection foreach iterator"
catalog: true
tags:
    - collection
    - foreach
    - iterator
---
# foreach 循环与 iterator 迭代器遍历
## iterator 迭代器遍历
### iterator 接口方法
#### 上源码
```java
public interface Iterator<E> {
    /**
     * Returns {@code true} if the iteration has more elements.
     * (In other words, returns {@code true} if {@link #next} would
     * return an element rather than throwing an exception.)
     *
     * @return {@code true} if the iteration has more elements
     */
    boolean hasNext();

    /**
     * Returns the next element in the iteration.
     *
     * @return the next element in the iteration
     * @throws NoSuchElementException if the iteration has no more elements
     */
    E next();

    /**
     * Removes from the underlying collection the last element returned
     * by this iterator (optional operation).  This method can be called
     * only once per call to {@link #next}.  The behavior of an iterator
     * is unspecified if the underlying collection is modified while the
     * iteration is in progress in any way other than by calling this
     * method.
     *
     * @implSpec
     * The default implementation throws an instance of
     * {@link UnsupportedOperationException} and performs no other action.
     *
     * @throws UnsupportedOperationException if the {@code remove}
     *         operation is not supported by this iterator
     *
     * @throws IllegalStateException if the {@code next} method has not
     *         yet been called, or the {@code remove} method has already
     *         been called after the last call to the {@code next}
     *         method
     */
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
}
```
#### 接口实现
Java中提供了一个 Iterable 接口，Iterable 接口实现后的功能是通过 iterator() 方法“返回”一个迭代器，而因为 Collection 继承了 Iterable 接口，所以所有继承了 Collection 的集合都可以通过迭代器进行循环遍历

简单示例：
```java
    Iterator it = collection.iterator(); // 获得一个迭代子
    while(it.hasNext()) {
        Object obj = it.next(); // 得到下一个元素
    }
```
### map 实现迭代器遍历
map 本身不是一个 Collection，但是它提供了一个 entrySet() 的方法用来遍历里面的内容，而很明显 entrySet() 返回的是一个 Set 集合继承了 Collection 自然也就支持迭代器遍历，示例如下：
```java
    Map<String,String> map = new HashMap<>();
    map.put("testkey","testValue");
    Iterator<Map.Entry<String,String>> iterator = map.entrySet().iterator();
    while (iterator.hasNext()) {
      Map.Entry<String,String> entry = iterator.next();
      System.out.println("key:" + entry.getKey());
      System.out.println("value:" + entry.getValue());
      iterator.remove();
    }
    System.out.println("map.size:" + map.size());
```
输出：
```
key:testkey2
value:testValue2
key:testkey1
value:testValue1
map.size:0
```
## foreach 循环遍历
for each 可以用来处理集合中的每个元素而不用考虑集合定下标。

格式如下 
```java
for(variable:collection){ statement; }
```

定义一个变量用于暂存集合中的每一个元素，并执行相应的语句(块)。collection 必须是一个数组或者是一个实现了 lterable 接口的类对象。

示例代码
```java
    Map<String,String> map = new HashMap<>();
    map.put("testkey1","testValue1");
    map.put("testkey2","testValue2");
    for (Map.Entry<String,String> entry : map.entrySet()) {
      System.out.println("key:" + entry.getKey());
      System.out.println("value:" + entry.getValue());
      map.remove(entry.getKey());
    }
    System.out.println("map.size:" + map.size());
```

注意，这里给了一个错误的示例 
```java 
map.remove(entry.getKey());
```
这也是接下来要说的 foreach 与 Iterator 的区别

输出结果：
```
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.HashMap$HashIterator.nextNode(HashMap.java:1437)
	at java.util.HashMap$EntryIterator.next(HashMap.java:1471)
	at java.util.HashMap$EntryIterator.next(HashMap.java:1469)
	at com.iflytek.gnome.adx.Candy.main(Candy.java:26)
key:testkey2
value:testValue2
```
## foreach 与 Iterator 的区别