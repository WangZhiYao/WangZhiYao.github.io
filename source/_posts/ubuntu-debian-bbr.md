---
title: Ubuntu 或 Debian 开启 BBR
date: 2021-02-05 11:57:02
toc: true
categories: 
 - Linux
tags: 
 - Ubuntu
---

需要 Linux 内核 4.9 及以上版本

### 1.修改系统变量

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

### 2.保存生效

```bash
sysctl -p
```

### 3.查看是否已经开启

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

如输出如下，含有 `bbr` 即可，无需在意顺序，则证明已开启

```bash
net.ipv4.tcp_available_congestion_control = reno cubic bbr
```

### 4.查看BBR是否启动

```bash
lsmod | grep bbr
```

显示以下即启动成功：

```bash
tcp_bbr                20480  20
```