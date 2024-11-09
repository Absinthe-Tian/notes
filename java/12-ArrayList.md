---
title: 12.Java集合
date: 2024-03-29 10:38:21
categories: Java语言
tags:
  - 集合
  - ArrayList
---

Java集合框架是Java编程语言中用于存储、组织和操作数据的一组类和接口。它提供了各种实现和数据结构，以满足不同类型的数据存储和操作需求。

1. **Collection接口**：Collection是所有集合类的根接口，它定义了一组通用的方法，可以用于操作集合中的元素。常见的子接口包括List、Set和Queue。
2. **List接口**：List是有序集合，允许重复元素。它的实现类包括ArrayList、LinkedList和Vector等。ArrayList基于数组实现，支持随机访问和动态扩展；LinkedList基于链表实现，支持快速插入和删除；Vector与ArrayList类似，但是线程安全。
3. **Set接口**：Set是不允许重复元素的集合，它的实现类包括HashSet、TreeSet和LinkedHashSet等。HashSet基于哈希表实现，具有常数时间的添加、删除和查找操作；TreeSet基于红黑树实现，元素按照自然顺序或指定比较器排序；LinkedHashSet继承自HashSet，内部使用链表维护插入顺序。
4. **Queue接口**：Queue是一种先进先出（FIFO）的数据结构，它的实现类包括LinkedList和PriorityQueue。LinkedList可以作为队列或栈使用；PriorityQueue基于优先级堆实现，元素按照优先级顺序出队。
5. **Map接口**：Map是键值对的集合，每个键唯一且与一个值关联。它的实现类包括HashMap、TreeMap、LinkedHashMap和Hashtable等。HashMap基于哈希表实现，提供快速的键值查找；TreeMap基于红黑树实现，键按照自然顺序或指定比较器排序；LinkedHashMap继承自HashMap，内部使用链表维护插入顺序；Hashtable与HashMap类似，但是线程安全。
