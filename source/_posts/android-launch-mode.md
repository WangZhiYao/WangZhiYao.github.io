---
title: Android 启动模式
date: 2018-09-14 01:10:55
categories: 
 - Android
tags: 
 - Framework
---

## 1.Standard

这个模式是默认的启动模式，即标准模式，在不指定启动模式的前提下，系统默认使用该模式启动 Activity ，每次启动一个 Activity 都会重写创建一个新的实例，不管这个实例存不存在，这种模式下，谁启动了该模式的 Activity ，该 Activity 就属于启动它的 Activity 的任务栈中。这个 Activity 它的 `onCreate()`，`onStart()`，`onResume()` 方法都会被调用。

## 2.SingleTop

这个模式下，如果新的 Activity 已经位于栈顶，那么这个 Activity 不会被重写创建，同时它的 `onNewIntent()` 方法会被调用，通过此方法的参数我们可以去除当前请求的信息。如果栈顶不存在该Activity的实例，则情况与 standard 模式相同。需要注意的是这个Activity它的 `onCreate()`，`onStart()` 方法不会被调用，因为它并没有发生改变。

* 当前栈中已有该 Activity 的实例并且该实例位于栈顶时，不会新建实例，而是复用栈顶的实例，并且会将 Intent 对象传入，回调 `onNewIntent()` 方法。
* 当前栈中已有该 Activity 的实例但是该实例不在栈顶时，其行为和 Standard 启动模式一样，依然会创建一个新的实例。
* 当前栈中不存在该 Activity 的实例时，其行为同 Standard 启动模式。

## 3.SingleInstance

该模式也是单例的，但和 SingleTask 不同， SingleTask 只是任务栈内单例，系统里是可以有多个 SingleTask Activity 实例的，而 SingleInstance Activity 在整个系统里只有一个实例，启动一 SingleInstanceActivity 时，系统会创建一个新的任务栈，并且这个任务栈只有他一个Activity。

## 4.SingleTask

该模式的 Activity 在同一个 Task 内只有一个实例，如果 Activity 已经位于栈顶，系统不会创建新的 Activity 实例，和 SingleTop 模式一样。但 Activity已经存在但不位于栈顶时，系统就会把该 Activity 移到栈顶，并把它上面的 Activity 出栈。