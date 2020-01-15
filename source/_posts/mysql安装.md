---
title: mysql安装
date: 2019-12-24 21:23:39
tags: mysql
category: mysql
---

### Linux上Mysql安装

1. 下载并安装mysql官方的yum Repository

在Linux中yum就是包管理软件，可以用来安装各种软件并解决各种依赖，**yum的下载软件的地方称作yum源**，软件的来源，是在/etc/yum.repose.d/目录中

![](1.png)

这些repo文件中就是记录了软件下载的来源，不同软件下载来源肯定不一样，所以在软件的官网都会提供yum源来下载

```shell
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

下载的是yum源文件，还要安装到本地，才能用该源去下载mysql

```shell
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

安装mysql服务器

```shell
yum -y install mysql-community-server
```

此时下载安装的mysql就是从yum源中的地址来的

2. mysql数据库设置

启动MySQL

```shell
systemctl start  mysqld.service
```

查看MySQL运行状态

```shell
systemctl status mysqld.service
```

**此时MySQL已经开始正常运行，需要找出root的密码**

因为初始化的mysql是随机密码必须先登陆才能修改

```shell
grep "password" /var/log/mysqld.log
```

如下命令登录mysql

```shell
mysql -uroot -p
```

登陆之后第一件事是修改密码为123456

```mysql
set password for root@localhost = password('123456');
```

这时会提示密码过于简单无法创建，需要需改创建密码规则

```mysql
set global validate_password_policy=0;
set global validate_password_length=1;
```

然后就可以正常设置了

解决远程登陆问题

```mysql
#任何主机
mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

#指定主机
mysql>GRANT ALL PRIVILEGES ON *.* TO 'jack'@’10.10.50.127’ IDENTIFIED BY '654321' WITH GRANT OPTION;

# 然后刷新权限
mysql>flush privileges;



```



为了支持中文需要修改编码为utf-8

```shell
vim /etc/my.cnf
```

```shell
[client]
port=3306
socket=/var/lib/mysql/mysql.sock
default-character-set=utf8

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
character-set-server=utf8

[mysql]
no-auto-rehash
default-character-set=utf8

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

```

重启mysql服务

```shell
service mysqld restart
```

查看数据库编码

```mysql
show variables like 'character_set%';
```

