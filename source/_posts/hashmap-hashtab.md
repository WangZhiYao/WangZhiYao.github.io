---
title: Java HashMap 与 Hashtable 的区别
date: 2020-03-19 21:57:11
categories: 
 - Code
tags: 
 - HashMap
---

## 1.HashMap

HashMap 不是线程安全的，HashMap 是 Map 接口的实现类，是将键映射到值的对象，其中键和值都是对象，并且不能包含重复键，但可以包含重复值。HashMap 允许 null key 和 null value， HashMap 是 HashTable 的轻量级实现 ，由于非线程安全，效率可能会比 Hashtable 高。

需要线程安全的 HashMap 可以使用 **ConcurrentHashMap**，其中 put 方法使用了 synchronized 加上线程锁。

## 2.Hashtable

Hashtable 是线程安全的，是 Map 接口的实现类，不允许 null key 和 null value，主要方法都使用了 synchronized 线程锁。

## 3.主要区别

* HashMap允许将 null 作为一个 entry 的 key 或者 value，而 Hashtable 不允许。
* HashMap 把 Hashtable 的 contains 方法去掉了，改成 containsValue 和 containsKey。因为 contains 方法容易让人引起误解。
* Hashtable 继承自 Dictionary，而 HashMap 继承自 AbstractMap，而 AbstractMap 是 Java1.2 引进的 Map 接口的一个实现类。
* Hashtable 的方法是线程安全的，而 HashMap 不是，在多个线程访问 Hashtable 时，不需要自己为它的方法实现同步，而 HashMap 就必须手动提供外同步。
* HashTable中的 initialCapacity 初始大小是11，增加的方式是 (oldCapacity << 1) + 1 即为 oldCapacity * 2 + 1。HashMap 中 initialCapacity 数组的默认大小是 16，增加的方式是 oldCapacity << 1。 loadFactor 同为 0.75。

