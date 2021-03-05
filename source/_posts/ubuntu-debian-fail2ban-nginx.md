---
title: Ubuntu 或 Debian 在 Nginx 下使用 fail2ban 阻止恶意扫描
date: 2021-02-23 18:19:03
categories: 
 - Linux
tags:
 - Ubuntu
 - fail2ban
 - Nginx
---

在 `/etc/fail2ban/filter.d` 下新建 `nginx-cc.conf`

```bash
touch /etc/fail2ban/filter.d/nginx-cc.conf
```

输入：

```bash
[Definition]
failregex = ^<HOST> -.*"(GET|POST|HEAD).*" 404 .*$
ignoreregex =.*(robots.txt|favicon.ico|jpg|png)
```

然后在 `/etc/fail2ban/jail.d/` 的 `defaults-debian.conf` 中加入如下几行：

```bash
[nginx-botsearch]
enabled = true
[nginx-cc]
enabled = true
filter  = nginx-cc
logpath = %(nginx_access_log)s
port    = http,https
```