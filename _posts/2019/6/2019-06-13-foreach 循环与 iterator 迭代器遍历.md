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
### remove 操作的不同
可以看出,使用 for each 循环语句的优势在于更加简洁，更不容易出错，不必关心下标的起始值和终止值。

forEach 不是关键字,关键字还是 for,语句是由 iterator 实现的，他们最大的不同之处就在于 remove() 方法上。

如果在循环的过程中调用集合的 remove() 方法，就会导致循环出错，因为循环过程中 Collection.size() 的大小变化了，就导致了错误。 所以，如果想在循环语句中删除集合中的某个元素，就要用迭代器 iterator 的 remove() 方法，因为它的 remove() 方法不仅会删除元素，还会维护一个标志，用来记录目前是不是可删除状态，例如，你不能连续两次调用它的remove()方法，调用之前至少有一次next()方法的调用。

forEach就是为了让用iterator循环访问的形式简单，写起来更方便。当然功能不太全,所以但如有删除操作，还是要用它原来的形式。

### 性能对比
举个例子：

采用 ArrayList 对随机访问比较快，而 for 循环中的 get() 方法，采用的即是随机访问的方法，因此在 ArrayList 里，for 循环较快

采用 LinkedList 则是顺序访问比较快，iterator 中的 next() 方法，采用的即是顺序访问的方法，因此在 LinkedList 里，使用 iterator 较快

从数据结构角度分析, for 循环适合访问顺序结构,可以根据下标快速获取指定元素.而 Iterator 适合访问链式结构,因为迭代器是通过 next() 和 Pre() 来定位的.可以访问没有顺序的集合.

而使用 Iterator 的好处在于可以使用相同方式去遍历集合中元素，而不用考虑集合类的内部实现（只要它实现了 java.lang.Iterable 接口），如果使用 Iterator 来遍历集合中元素，一旦不再使用 List 转而使用 Set 来组织数据，那遍历元素的代码不用做任何修改，如果使用 for 来遍历，那所有遍历此集合的算法都得做相应调整,因为 List 有序, Set 无序,结构不同,他们的访问算法也不一样.