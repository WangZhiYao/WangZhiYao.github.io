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

{% codeblock "nginx-cc.conf" lang:bash %}
[Definition]
failregex = ^<HOST> \- \S+ \[\] \".*\" (400|404|444) .+$
ignoreregex =.*(jpg|png)
{% endcodeblock %}

然后在 `/etc/fail2ban/jail.d/` 的 `defaults-debian.conf` 中加入如下几行：

{% codeblock "defaults-debian.conf" lang:bash %}
[nginx-botsearch]
enabled = true
[nginx-cc]
enabled = true
filter  = nginx-cc
logpath = %(nginx_access_log)s
port    = http,https
{% endcodeblock %}

unban：

```bash
fail2ban-client set jailname unbanip ipaddress
```

规则校验：

```bash
fail2ban-regex /var/log/nginx/*.access.log /etc/fail2ban/filter.d/nginx-cc.conf
```

