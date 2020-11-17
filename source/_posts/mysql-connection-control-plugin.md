---
title: MySQL 连接控制插件
date: 2019-04-27 15:53:29
categories: 
 - Linux
tags: 
 - MySQL
---

最近查看服务器日志发现一堆IP尝试爆破我的 MySQL，我的数据库端口并没有修改，问题并不大，但是让这些IP无代价的爆破生成一堆日志让我很烦，所以查了一下各种解决方案，有提到 Fail2Ban，但是我服务器已经装了 DenyHosts，所以并不想再引入一个功能上有重复的包，所以使用了MySQL的 `Connection-Control` 插件，下面是它官网的描述：

<!-- more -->

> As of MySQL 5.7.17, MySQL Server includes a plugin library that enables administrators to introduce an increasing delay in server response to clients after a certain number of consecutive failed connection attempts. This capability provides a deterrent that slows down brute force attacks that attempt to access MySQL user accounts.

这个插件库包含两个插件：

* CONNECTION_CONTROL : 用来控制登录失败的次数及延迟响应时间
* CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS : 该表将登录失败的操作记录至 `information_schema` 库中

## 1.安装

```mysql
mysql> INSTALL PLUGIN CONNECTION_CONTROL SONAME 'connection_control.so';
mysql> INSTALL PLUGIN CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS SONAME 'connection_control.so';
```

## 2.查看是是否安装与开启状态

```mysql
mysql> SELECT PLUGIN_NAME, PLUGIN_STATUS FROM INFORMATION_SCHEMA.PLUGINS WHERE PLUGIN_NAME LIKE 'connection%';
```
输出类似如下：

| PLUGIN_NAME                              | PLUGIN_STATUS |
| ---------------------------------------- | ------------- |
| CONNECTION_CONTROL                       | ACTIVE        |
| CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS | ACTIVE        |

其中 **PLUGIN_STATUS** 状态为 **ACTIVE** 则证明插件都已经开启。

## 3.查看参数

```mysql
mysql> SHOW VARIABLES LIKE '%connection_control%';
```
输出类似如下：

| Variable_name                                   | Value    |
| ----------------------------------------------- | -------- |
| connection_control_failed_connections_threshold | 3        |
| connection_control_max_connection_delay         | 86400000 |
| connection_control_min_connection_delay         | 300000   |

其中：

* **connection_control_failed_connections_threshold** 代表失败尝试的次数，默认为3，表示当连接失败3次后启用连接控制，0表示不开启。
* **connection_control_max_connection_delay** 单位为 **毫秒** ，最大响应延迟的时间，默认约25天，我的设置是1天。
* **connection_control_min_connection_delay** 单位为 **毫秒**，**每次** 响应延迟的 **最小增量**，默认1000毫秒，我的设置是5分钟。

## 4.修改参数

```mysql
mysql> SET GLOBAL connection_control_failed_connections_threshold = 1;
mysql> SET GLOBAL connection_control_max_connection_delay = 86400000;
mysql> SET GLOBAL connection_control_min_connection_delay = 300000;
```

 **通过以上方式重新设置参数或者重启数据库会导致配置还原，同时，`CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS` 中的数据也会被清空，当前控制连接的次数也会被重置**

**建议通过在配置文件中的 `[mysqld]` 下加入以下内容的方式设置参数**，这样才能保证即使重启也不需要重新设置：

```
# Connection Control
plugin-load-add = connection_control.so
connection_control_failed_connections_threshold = 1
connection_control_max_connection_delay = 86400000
connection_control_min_connection_delay = 300000
```

## 5.查看当前控制连接的次数

```mysql
mysql> SHOW GLOBAL STATUS LIKE 'Connection_control_delay_generated';
```

## 6.查看所有登录操作失败的记录

```mysql
mysql> use information_schema;
mysql> SELECT * FROM CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS;
```

## 7.卸载插件

```mysql
mysql> UNINSTALL PLUGIN CONNECTION_CONTROL;
mysql> UNINSTALL PLUGIN CONNECTION_CONTROL_FAILED_LOGIN_ATTEMPTS;
```

如果使用的是在配置文件中设置的参数，在卸载时也需要删除对应配置



