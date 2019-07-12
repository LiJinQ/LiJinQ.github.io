---
layout:     post
title:      Java面试系列（一）
subtitle:   Java集合21题
date:       2019-07-12
author:     LJQ
header-img: img/post-bg-rwd.jpg
catalog: true
tags:
    - Java
    - Collection
---

##### 1.ArrayList 和 Vector 的区别。

ArrayList线程不安全但是性能好，Vector线程安全但是性能不好。
ArrayList中的元素超过它初始大小时会增加50%容量，Vector会则会翻倍，因此ArrayList容易节省内存空间。

##### 2.说说 ArrayList,Vector, LinkedList 的存储性能和特性。

ArrayList和Vector都是使用数组存储数据，因为数组的特性，所以ArrayList和Vector都属于索引数据快但是插入数据慢，ArrayList线程不安全，Vector线程安全，因此ArrayList性能比Vector更好。
LinkedList是双链表结构，因为链表的特性，内存利用率更高，但是索引需要遍历链表，所以其插入数据快但是索引数据慢。

##### 3.快速失败 (fail-fast) 和安全失败 (fail-safe) 的区别是什么？

- 快速失败（fail-fast）
当迭代器在遍历一个集合对象时，如果期间集合发生了增删改操作时会抛出Concurrent Modification Exception异常。java.util下的包都是快速失败的，因此不能在多线程下发生并发修改。
- 安全失败（fail-safe）
采用安全失败的集合容器不会在原容器上遍历，而是先复制在进行遍历，因此期间如果修改了集合内容，遍历是不会抛出异常的，java.util.concurrent包下的容器都是安全失败，可以在多线程下并发修改。

##### 4.hashmap 的数据结构。

jdk1.8以前是数组+链表。
jdk1.8以后是数组+链表+红黑树

##### 5.HashMap 的工作原理是什么?

- put
根据key值计算出hashcode，再根据hashcode通过hash算法算出数组下标，数组中的每一个元素都是链表，当hash冲突时，该位置的链表将生成一个新的节点。

- get
根据key值计算出hashcode，再根据hashcode通过hash算法算出数组下标，如果该位置存在hash冲突，则从该节点开始遍历链表或红黑树。

##### 6.Hashmap 什么时候进行扩容呢？

当hashmap中的元素个数超过阈值时，这个值一般是数组大小*loadFactor（默认为0.75）

##### 7.List、Map、Set 三个接口，存取元素时，各有什么特点？

- List
存元素：调用add(object)方法时会按顺序插入，调用add(int index,object)方法可以按下标插入。
取元素：1、Iterator接口取得所有元素，逐一遍历。
2、调用get(index)方法，根据下标查找。

- Map
存元素：调用put(Object key,Object value)方法，不能存入相同的key。
取元素：1、调用get(Object key)方法，通过key值获取value。
2、获取所有key值集合或所有value值集合。
3、获得Map.Entry<K,V>集合，遍历Entry。

- Set
存元素：调用add(Object)方法，当集合中有该元素时，插入失败，返回false；若不存在该元素，插入成功，放回true。
取元素：Iterator接口取得所有元素，逐一遍历。

##### 8.Set 里的元素是不能重复的，那么用什么方法来区分重复与否呢? 是用 == 还是 equals()? 它们有何区别?

使用equals方法区分。“==”在比较基本数据类型时是比较数值，在比较对象时，比较的是指向的对象的地址；“equals”比较的是是否同一个对象，如果没有对equals方法进行重写，比较的是对象指向的地址，如果重写（如String，Date）equals方法，比较的是对象的内容。因此，在比较int，double等基本数据类型时，可以使用“==”，在比较String，Date等引用数据类型时，使用“equals”。

##### 9.两个对象值相同 (x.equals(y) == true)，但却可有不同的 hash code，这句话对不对?

不对，首先基本数据类型没有equals方法，比较的是没有重写equals方法的对象的话，比较的内容就是hashcode；而重写了equals方法的引用数据类型（如String），比较的是内容，而java中，相同的内容指向同一个hashcode，因此，如果 (x.equals(y) == true)，其hashcode也会相同。

##### 10.heap 和 stack 有什么区别。

从存储角度看，堆存储实例对象，栈存储基本变量和引用对象；从存取速度看，堆存取速度慢，栈存取速度快；从线程看，每个线程都有一个Java栈，所有线程共享一个堆；从GC看，堆不频繁，栈比较频繁。

##### 11.Java 集合类框架的基本接口有哪些？

    1.Collection：单列集合的根接口
        1.List：有序集合，可重复
            1.ArrayList：有序集合，数组实现，易索引
            2.LinkedList：有序集合，双链表实现，易插入
        2.Set：无序集合，不可重复
            1.HashSet：根据对象hash值确定元素在集合中的位置
            2.TreeSet：以二叉树方式存放元素，实现对集合中元素的排序
    2.Map：双列集合的根接口，存放<key,value>键值对
        1.HashMap：存放<key,value>键值对，不能出现重复的key
        2.TreeMap：存放<key,value>键值对，不能出现重复的key，按二叉树方式排列

##### 12.HashSet 和 TreeSet 有什么区别？

HashSet内部是hash表，根据hash值确定元素在集合中的位置；TreeSet内部是二叉树，对集合元素实现排序。

##### 13.HashSet 的底层实现是什么?

HashSet由一个Hash表支持，实际上是一个HashMap实例，因为HashMap的Key值是不能重复的，因此值都存为key，而value则是一个虚拟的Object对象。

##### 14.LinkedHashMap 的实现原理?

LinkedHashMap继承了HashMap的特性，不同的是，HashMap中本来的节点被串联成了双向链表，因此，LinkedHashMap在遍历时可以有序输出，而不是无序输出。

##### 15.为什么集合类没有实现 Cloneable 和 Serializable 接口？

克隆或者序列化的语义和含义是跟具体的实现相关的。因此应该由集合类的具体实现类来决定如何被克隆或者序列化。

##### 16.什么是迭代器 (Iterator)？

Iterator提供了同意遍历操作集合元素的统一接口。

##### 17.Iterator 和 ListIterator 的区别是什么？

     1.使用范围不同，Iterator可以适用于所有集合，ListIterator只能适用于List及其子类型。
     2.Iterator只能实现删除操作，而ListIterator可以实现增删改操作。
     3.ListIterator可以实现逆向遍历。
     4.ListIterator可以定位当前索引位置。

##### 18.数组 (Array) 和列表 (ArrayList) 有什么区别？什么时候应该使用 Array 而不是 ArrayList？

Array 可以包含基本类型和对象类型，ArrayList 只能包含对象类型。 Array 大小是固定的，ArrayList 的大小是动态变化的。
ArrayList 提供了更多的方法和特性，比如:addAll()，removeAll()，iterator()等等。 对于基本类型数据，集合 使用自动装箱来减少编码工作量。但是，当处理固定大小的基本数据类型的时候，这种方式相对比较慢。

##### 19.Java 集合类框架的最佳实践有哪些？

    1.根据应用需要正确选择要使用的集合类型对性能非常重要，比如：假如知道元素的大小是固定的，那么选用Array类型而不是ArrayList类型更为合适。
    2.有些集合类型允许指定初始容量。因此，如果我们能估计出存储的元素的数目，我们可以指定初始容量来避免重新计算hash值或者扩容等。
    3.为了类型安全、可读性和健壮性等原因总是要使用泛型。同时，使用泛型还可以避免运行时的ClassCastException。
    4.使用JDK提供的不变类(immutable class)作为Map的键可以避免为我们自己的类实现hashCode()和equals()方法。
    5.编程的时候接口优于实现
    6.底层的集合实际上是空的情况下，返回为长度是0的集合或数组而不是null。

##### 22.Comparable 和 Comparator 接口是干什么的？列出它们的区别

链接：[https://www.nowcoder.com/questionTerminal/596ede1e92ac43f1b4ca81e96fe798e7?orderByHotValue=1&page=1&onlyReference=false](https://www.nowcoder.com/questionTerminal/596ede1e92ac43f1b4ca81e96fe798e7?orderByHotValue=1&page=1&onlyReference=false)
来源：牛客网

Comparable和Comparator接口是用来对自定义的class比较大小的。
Comparator和 Comparable的区别：Comparator定义在Person的外部而Comparable定义在Person的内部。
Comparable定义在Person的内部。public class Persion implements Comparable {..比较Person的大小..}，因为已经实现了比较器，那么Person现在是一个可以比较大小的对象了，它的比较功能和String完全一样，可以随时随地的拿来比较大小，因为Person现在自身就是有大小之分的。Collections.sort(personList)可以得到正确的结果。
Comparator是定义在Person的外部的，此时Person类的结构不需要有任何变化，如public class Person{ String name; int age }，然后另外定义一个比较器：public PersonComparator implements Comparator() {..比较Person的大小..}，在PersonComparator里面实现了怎么比较两个Person的大小. 所以用这种方法，要对一个 personList进行排序的时候除了要传递personList过去，还需要把PersonComparator传递过去，因为怎么比较Person的大小是在PersonComparator里面实现的，如Collections.sort( personList , new PersonComparator() )。


##### 21.Collection 和 Collections 的区别。

- Collection 是一个集合接口（集合类的一个顶级接口）。它提供了对集合对象进行基本操作的通用接口方法。
- Collections则是集合类的一个工具类/帮助类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。

---

**题目来源于微信公众号--互联网架构师**
![](https://upload-images.jianshu.io/upload_images/15504753-58113568a7daf39c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
