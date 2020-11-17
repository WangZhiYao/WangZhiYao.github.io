---
title: Java 中赋值表达式的返回值
date: 2020-11-07 06:46:53
tags: 
 - Framework
categories: 
 - Java
---

# 起因

写代码的时候脑子里想着

> 如果 flag = true 则 do something ...

于是手上跟着敲出
```java
if (flag = true) {
    // do something ...
}
```

编译器没有报错，当时思想走神也丝毫没有发觉这是一段弱智代码，调试的时候发现 if 里的语句每次都执行了，觉得不太对劲，然而仍然没有发现这段弱智代码有问题，本着格物致知的精神格了几分钟才发现这段代码应该是：

```java
if (flag) {
    // do something ...
}
```

瞬间觉得自己宛如智障

<!-- more -->

# 经过

google 了一下 java 赋值表达式，才发现赋值表达式也是有返回值的，突然就想起：

```java
while((len = in.read(buffer)) != -1) {
    ...
}
```

这点在 [Oracle Java 文档](https://docs.oracle.com/javase/specs/jls/se13/html/jls-15.html#jls-15.26) 中有描述

# 结果

赋值表达式的返回值是表达式中右边的值，`evaluation order`是从左到右也就是说：

```java
int i = 2;
int j = (i = 3) * i;
```

i 会先被赋值为 3，再乘以本身，所以 j = 9

于是我当时写下的：

```java
if (flag = true) {
    // do something ...
}
```

if 表达式中的 flag 每次都会被我设为 true 且返回值也是 boolean 类型所以编译器不会报错，然后进入 if 中执行

这就是这次弱智事情的结果