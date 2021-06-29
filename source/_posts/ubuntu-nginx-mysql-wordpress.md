---
title: Ubuntu 下使用 Nginx + MySQL 搭建 Wordpress
date: 2021-02-05 11:54:17
toc: true
categories: 
 - Linux
tags: 
 - Ubuntu
 - Nginx
 - MySQL
 - Wordpress
---

**以 Ubuntu 20.04 为例，且假设你已获取到 root 权限或者使用的是 root 账号，Wordpress 与 MySQL 安装在同一台服务器上**

### 1. Wordpress

**如果你已经使用其他工具下载好了 Wordpress 则可以使用 sftp 工具或者任意工具上传到服务器 /var/www 文件夹下，跳过1.1**

#### 1.1 下载

```bash
curl https://cn.wordpress.org/latest-zh_CN.tar.gz -o /var/www/wordpress.tar.gz
```

该命令是将 **中文版** 的 Wordpress 下载到路径 /var/www 中，名字为 wordpress.tar.gz， 如果提示：

```bash
bash: curl: command not found.
```

则运行如下命令后再运行第一条命令下载 Wordpress：

```bash
apt install curl
```

<!-- more -->

#### 1.2 解压及修改用户组权限

首先进入 `/var/www` 文件夹

```bash
cd /var/www
```

解压刚刚下载的 wordpress.tar.gz 到当前文件夹中

```bash
tar -zxvf wordpress.tar.gz
```

修改文件夹用户组权限

```bash
chown -R www-data:www-data /var/www/wordpress
```


### 2. Nginx

#### 2.1 安装

```bash
apt install nginx -y
```

当上面命令执行完，访问你服务器的 ip 出现以下则证明安装成功

>**Welcome to nginx!**
>
>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
>
>For online documentation and support please refer to [nginx.org](http://nginx.org/).
>Commercial support is available at [nginx.com](http://nginx.com/).
>
>*Thank you for using nginx.*

如果发现访问超时，请检测是否因为 防火墙(ufw) 或者 服务器提供商 关闭了80端口

#### 2.2 配置

首先复制一份默认配置，将下面命令中的 `{your-domain}` 更换为你自己的域名或者别的名字

```bash
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/{your-domain}.conf
```

接着修改这份配置

```bash
vi /etc/nginx/sites-available/{your-domain}.conf
```

如果提示

```bash
bash: vi: command not found.
```

则使用如下命令安装 Vim，安装完成后再接着使用上面的命令修改配置

```bash
apt install vim
```

将配置修改成如下，如果没有使用域名则无需修改 `server_name` 字段

{% codeblock "{your-domain}.conf" lang:nginx %}
server {
  listen 80;
  listen [::]:80;

  root /var/www/wordpress;
  index index.php;

  server_name {you-domain};

  location / {
    try_files $uri $uri/ =404;
  }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
  }
}
{% endcodeblock %}

其中 `fastcgi_pass` 这一项中的 `php7.4-fpm.sock` 需要通过如下命令安装，系统不同，安装上的版本不同，记得**替换成你们对应的版本**

```bash
apt install php-fpm
```

删除正在使用的默认的配置

```bash
rm /etc/nginx/sites-enabled/default
```

将修改好的配置加入到正在使用的配置中

```bash
ln -s /etc/nginx/sites-available/{you-domain}.conf /etc/nginx/sites-enabled/
```

测试配置是否正确

```bash
nginx -t
```

如果出现如下则代表配置成功

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

重载配置以生效

```bash
nginx -s reload
```

此时去浏览器访问你的 ip 已经会出现如下提示：

> 您的PHP似乎没有安装运行WordPress所必需的MySQL扩展。

则还需要安装 `php-mysql`

```bash
apt install php-mysql
```

此时距离完成就剩下安装数据库了


### 3. Mysql

#### 3.1 安装

```bash
apt install mysql-server
```

安装完成后输入如下命令

```bash
mysql_secure_installation
```

如果显示：

```bash
Securing the MySQL server deployment.

Enter password for user root:
```

如果在安装的时候设置过 `root` 的密码，则输入密码后再 `Enter`，否则直接 `Enter` 跳过

接着：

```bash
VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No:
```

输入 `y` 然后 `Enter`

```bash
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:
```

这里是设置密码强度校验，根据个人的密码强度选择即可

```bash
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) :
```

删除匿名用户，输入 `y` 然后 `Enter`

```bash
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) :
```

是否禁止 `root` 远程登录，一般选是，输入 `y` 然后 `Enter`

```bash
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) :
```

是否删除测试数据库，选是，输入 `y` 然后 `Enter`

```bash
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) :
```

是否立即重新加载权限，选是，输入 `y` 然后 `Enter`

```bash
Success.

All done!
```

数据库初始化设置完成，输入以下命令进入数据库：

```bash
mysql -uroot -p
```

提示要输入密码：

```bash
Enter password:
```

输入前面设置的 `root` 的密码，然后 `Enter`，如果出现如下则证明成功进入数据库

```bash
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.23-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

然后创建数据库

```sql
CREATE DATABASE IF NOT EXISTS wordpress DEFAULT CHARACTER SET = utf8mb4 DEFAULT COLLATE = utf8mb4_general_ci;
```

提示如下则证明数据库创建成功

```bash
Query OK, 1 row affected (0.12 sec)
```

创建一个数据库用户 `wordpress` 用来操作 上面创建的 `wordpress` 数据库，修改 `{password}` 为你自己的密码

```sql
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY '{password}';
```

给用户 `wordpress` 授予 数据库 `wordpress` 的所有权限

```sql
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'localhost' WITH GRANT OPTION;
```

刷新权限

```sql
FLUSH PRIVILEGES;
```

退出数据库

```sql
exit
```

### 4. End

回到浏览器，刷新，点击 **现在就开始！** 

在下一个界面中，数据库名与用户名无需修改，只需要填入刚才设置的数据库密码点击提交即可

再接下来的步骤交给著名的WordPress五分钟安装程序