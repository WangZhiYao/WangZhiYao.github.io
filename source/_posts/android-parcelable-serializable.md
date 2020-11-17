---
title: Android 中的 Parcelable 与 Serializable
date: 2019-02-28 10:22:14
categories: 
 - Android
tags: 
 - Framework
---

## 1.作用

Serializable 的作用是为了保存对象的属性到本地文件、数据库、网络流、rmi 以方便数据传输，当然这种传输可以是程序内的也可以是两个程序间的。

而 Android 的 Parcelable 的设计初衷是因为 Serializable 效率过慢。为了在程序内不同组件间以及不同 Android 程序(AIDL)高效的传输数据而设计，这些数据仅在内存中存在，Parcelable 是通过 IBinder 通信的消息的载体。

## 2.区别

两者最大的区别在于存储媒介的不同，Serializable 使用 I/O 读写存储在硬盘上，而 Parcelable 是直接在内存中读写。很明显，内存的读写速度通常大于 I/O 读写，所以在 Android 中传递数据优先选择 Parcelable。

Serializable 会使用反射，序列化和反序列化过程需要大量的 I/O 操作，会在序列化的时候创建许多临时对象，容易触发GC。

Parcelable 自已实现封送和解封（marshalled &amp; unmarshalled）操作不需要用反射，数据也存放在 Native 内存中，效率要快很多。