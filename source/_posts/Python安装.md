---
title: Python安装
date: 2019-11-13 19:23:47
tags: Python
categories: Python
---

### Linux上安装python
目前Linux中都自带python2，使用

	python --version
查看python版本
>python2已成过去，以后不会有支持，建议不用，但是Linux中有依赖python2，所以也不能卸载，直接安装python3即可

### 安装前准备

	cd /usr/local/
	mkdir python3
此目录作为Python的主目录

1. 安装GCC编译器


		gcc --version
查看是否安装，若没有安装，则


		yum -y install gcc

2. 安装其他依赖包

		yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-develreadline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel

### 下载安装包


	cd python3/
	wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz

### 解压

	tar -xzvf Python-3.7.0.tgz
>tar命令报错，可能是没有安装tar，直接yum -y install tar

使用

	tree -d -L 1 Python-3.7.0
可以使用树形查看目录，更清楚
>若没有该命令则，yum -y install tree

### 开始安装
进入到刚解压出来的文件中即Python -3.7.0
#### 将创建的文件夹设置为程序主目录
意思是：安装的目录就是这个指定的目录，相关文件全放在这里

	./configure --prefix=/usr/local/python3

### 编译安装
还是在刚才目录
	
	make && make install

### 建立软连接
如果不建立连接，则只有在安装的目录中才能使用Python,建立连接是要在任何地方都能使用，要达到这个目的，还需要环境变量，即在任何地方使用命令，都会在环境变量路径中查找该命令

	ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3

	ln -s /usr/local/python3/pip3.7 /usr/bin/pip3
>pip是python的包管理工具，相当于nodejs中的npm

**安装成功后此Linux中就有两个python 版本2和版本3，现在只想使用版本3**

	file /usr/bin/python
输出打印

	/usr/bin/python: symbolic link to `python2'
表示输入python实际上运行的是python2

	which python2
找到Python2安装的位置，在环境变量的路径中找，python3也一样

	mv /usr/bin/python /usr/bin/python_bk
将该路径去除，实际上是改名了，然后就可以将Python3连接过来

	ln -s /usr/bin/python3 /usr/bin/python
>/usr/bin/python就相当于python

	python -V
	Python 3.7.0


file命令是查看一个文件的属性，可以看到它的连接，然后依次往下查找就可以找到唯一的运行文件，因为连接也是文件

ln是创建连接，源文件  目标文件

which是在环境变量中查找文件


链接（link）：系统中的链接是一个已经存在的文件的另一个名字，它不复制文件的内容。有两种链接方式，一种是硬链接（hard link），另一种是符号链接（symbolic link），又称软链接。硬链接和原有文件是存储在同一物理地址的两个不同的名字，因此硬链接是相互的；符号链接的内容只是一个所链接文件的文件名，在使用ls –l时，符号链接的第一项的第一位为“l”。 

指向一个文件的所有 硬链接都删掉的话文件的内容才会被删掉
软链接只要删掉了源链接文件，软链接也就失效了

ln和file在查看上是相反的，ln将存在的文件创建一个新名字，file是显示目前查看的文件的本名字是谁