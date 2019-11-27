---
title: git基本操作
date: 2019-10-26 15:48:20
tags:
- git&github
categories: 
- 工具
---

## 原理

![](image1.png)

### 运行前配置

	git config --global user.name "shuihan"
	git config --global user.email lxq_xsyu@163.com
该信息用于每次**本地提交**也即是commit时记录提交人的信息，不是上传至github的账号密码

### 查看配置信息
	git config --list
### 获取帮助
	git help <verb>
git的命令都是git+想要操作的范围+操作如：git remote -v
>-后面跟首字母缩写，--后面跟整个单词

## 基础

### 获取仓库

仓库就是一个文件夹，在任意文件夹内输入
	git init
就可以将此文件夹变成仓库

克隆仓库

	git clone git://github.com/test/gittest.git
git clone 后面跟仓库全地址，即可将整个仓库克隆下来这样默认目录会是gittest,如果要更改目录名称可以追加在后面。

	git clone git://github.com/test/gittest.git newname

### 查看文件状态

	git status
课显示出工作目录中有哪些变化的文件

### 跳过暂存区

	git commit -a -m "注释"

### 添加远程仓库

	git commit add [自己随便起个仓库名字但通常会叫origin] [url]
>git commit add origin git://github.com/test/gittest.git

该操作是在本地绑定远程的仓库，以后可直接上传至github，地址可以使用两种协议，一种是https一种是ssh，https上传时候需要github的账号密码，ssh需要公钥

### 查看远程仓库

	git remote -v
>可以添加多个远程仓库

### 将本地仓库内容上传到远程仓库

	git push origin master
该命令会自动使用之前绑定的路径上传,origin是自己起的仓库别名，origin就代表了git://github.com/test/gittest.git这个url，master代表分支

### 删除远程仓库

	git remote rm origin

同样的origin就代表了仓库别名，实际是git://github.com/test/gittest.git

## 分支

### 分支的理解

假设此时我们的工作目录有三个文件，把它们暂存后提交

1. 暂存 git使用blob类型的对象存储了这些快照
>暂存就是add后的状态，实际上存储的是文件的快照

2. 提交 git commit -m "commit three files"


此时git仓库中有5个对象（三个文件快照内容的blob对象，一个记录着目录树内容及其各个文件对应blob对象索引的tree对象，一个指向tree对象的索引和其他提交信息数据的commit对象）
>一个文件一个快照即Blog对象，一次提交一个目录树和一个commit对象
>他们的关系是一层指向一层

![](image2.png)

3. 多做几次修改再提交 后一次提交的commit对象会指向前一次提交的commit对象

![](image3.png)


git中的分支实际上仅仅是一个指向commit对象的可变指针，git会使用master为分支的默认名字，每次提交后master（当前分支）都会自动向前移动。

![](image4.png)

>分支之间不会互相影响

4. 创建新分支


		git branch testing

![](image5.png)

那么git是如何知道你在哪个分支上工作呢？它保留着一个名为HEAD的特别指针

![](image6.png)

5. 切换分支

		 git checkout testing

### 分支管理

git branch命令可以列出当前所有分支清单， 当前分支带 * 号


	$ git branch
	* master
 	 work


删除本地分支 git branch -d <branch-name> 删除远程分支 git push <remote-name> --delete <branch-name>


	$ git branch -d work
	$ git push origin --delete work

>-d等于--delete