---
title: Hexo+github搭建个人博客系统
date: 2019-10-24 20:13:22
tags: hexo
categories: 博客
---


## 前期准备工作

### 安装Node.Js
下载地址 

	http://nodejs.cn/download/
	
	打开CMD命令行
	
	node -v
	npm -v
  查看是否安装成功（有版本显示则是成功），安装Node.Js主要是使用它的npm工具，使用npm可以下载软件，相当于使用git下载软件一样，是一个软件包管理平台

### 安装Git
下载地址 
	
	https://git-scm.com/downloads
无脑下一步安装好即可，使用Git主要是将写好的博客（文章）上传到Github平台上，以便可以使用网络访问
	
	git --version
查看是否安装成功，若有软件版本出来证明安装成功
>注意：git空客后是两个减号 --

### 在Github中创建此博客的仓库
![](1108615-20171021223639881-1998790649.png)

这里注意创建的仓库的名字必须是githu.io结尾，只要以此结尾github就默认知道该仓库是个人主页

---------------
**前期准备工作结束，下面开始安装软件**
### 安装Hexo
Hexo即时此次使用的博客系统，首先在选定的地方创建一个文件夹，我取名为blog(名字可任意)，该文件夹既是安装Hexo的地方，也是以后博客文章存放的地方，也是配置的地方

	npm install hexo -g
即可安装完成 输入
	
	hexo -v
可查看是否安装成功。若有版本显示，则安装成功<br>
>以后所有的操作全部在blog这个文件夹进行

输入

	hexo init
初始化该文件夹
>若有报错则可能是git没有安装，该初始化需要用到git

![](1108615-20171021230241646-1660449756.png)
**表示安装成功**

输入

		npm install
自动安装所需的组件	

输入

		hexo g
![](1108615-20171021231705474-1404994153.png)<br>
将blog文件下的所有博客文章全部生成，g代表generate（生成），此时只是生成，还没有部署

输入

		hexo s
开启服务器，即部署到本地
![](1108615-20171021231833912-663774637.png)<br>

**在浏览器中输入localhost:4000访问博客首页**<br>

![](1108615-20171021232413224-1288183746.png)<br>


**表示成功！**<br>
**至此本地博客系统已经搭建好了**

-------
### 将本地博客与github关联

如果电脑是第一次使用关联操作，则需要指定Git的用户名和邮箱<br>

>也是在blog文件夹中操作，在blog文件夹空白处右键选择git的bash命令

![](1108615-20171021233157224-1386748377.png)<br>

该用户名和邮箱是对一个Github上的，到时候关联需要验证<br>

#### 使用ssh将本地博客上传到github仓库中

输入

	cd ~/.ssh
查看是否有.ssh文件，该文件是ssh的密钥文件，公钥和私钥上传的时候需要用此来加密和解密，若没有则继续输入：

	ssh-keygen -t rsa -C “295048600@qq.com”

会在C:\Users\Administrator\.ssh中生成两个文件id_rsa和id_rsa.pub，pub的是公钥，上传的时候接收方需要知道公钥，即github需要拥有这台电脑的公钥才能上传<br>
>输入后连续回车即可创建

输入

	eval "$(ssh-agent -s)"
![](1108615-20171021234314146-695835137.png)<br>
添加密钥到ssh-agent<br>
在输入

	ssh-add ~/.ssh/id_rsa
![](1108615-20171021234528552-610835964.png)<br>
添加生成的SSH key到ssh-agent<br>

#### 登录Github,设置ssh公钥
![](1108615-20171021234636834-426105098.png)<br>

新建一个new ssh key，将id_rsa.pub文件里的内容复制上去<br>

![](1108615-20171021234906724-1938556332.png)<br>

输入

	ssh -T git@github.com
测试添加ssh是否成功<br>

![](1108615-20171021235116271-1521882533.png)


### 最后一步，在hexo中配置上传到github中的哪个仓库中

在blog文件夹中找到_config.yml文件，修改repo值
**直接在最后添加字段和值即可，注意每个冒号之后都要有一个空格，否则文件会报错**<br>
![](1108615-20171021235812974-84318377.png)<br>
>repository的值是下载该仓库十的地址 如下：

![](1108615-20171021235722365-818312042.png)

**至此环境搭建完毕！**
------
### 部署到github让大家访问

在cmd中输入

	hexo new post “博客名”
即可在blog/source/_posts目录下创建一个文章

![](1108615-20171022000508865-46787156.png)<br>

#### 在部署前安装一个扩展

	npm install hexo-deployer-git --save
忽略告警即可<br>

使用编辑器编辑好文章后使用：

	hexo d -g

即可完成部署，d是deploye(部署) -g是全局此时是部署到github上，通过ssh加密传输，其他没有私钥的主机无法上传

![](1108615-20171022001410662-1611125904.png)<br>
**部署成功后访问你的地址：http://用户名.github.io。那么将看到生成的文章**


![](1108615-20171022001738037-1195721153.png)<br>
**至此，搭建加部署全部完成**

在使用的时候<br>
先使用

	hexo clean
清除，再使用

	hexo g
在本地生成文章，最后使用

	hexo d -g
部署到github上去

### 容易犯错的地方
* 直接在blog/source/_post目录下右键新建文件并编辑，这样的文章无法生成，因为缺少相关配置，必须使用

		hexo new "文章名字"

* 插入图片

使用网络上图片的URL有时候不能显示图片，特别是部署到github后，所以需要使用插件上传图片，hexo博客图片的问题在于，markdown文章使用的图片路径和hexo博客发布时的图片路径不一致。需要安装插件，再blog或者hexo目录下执行：

	npm install hexo-asset-image --save
然后在blog下的_config.yml中修改

	post_asset_folder:true

完成安装后用hexo新建文章的时候会发现_posts目录下面会多出一个和文章名字一样的文件夹。图片就可以放在文件夹下面。
>之前新建的文章不会有该目录！

该目录的作用是将文章中需要用到的图片放入其中，在生成文章时hexo会将其和生成的文章一起放在public文件下。

使用时只需要：

	![logo](logo.jpg)
即可，不需要加前缀，因为默认在同名文件下查找！

* Markdownpad2秘钥


		邮箱：
	
		Soar360@live.com


		授权秘钥：
	
		GBPduHjWfJU1mZqcPM3BikjYKF6xKhlKIys3i1MU2eJHqWGImDHzWd	D6xhMNLGVpbP2M5SN6bnxn2kSE8qHqNY5QaaRxmO3YSMHxlv2EYpjd	wLcPwfeTG7kUdnhKE0vVy4RidP6Y2wZ0q74f47fzsZo45JE2hfQBFi	2O9Jldjp1mW8HUpTtLA2a5/sQytXJUQl/	QKO0jUQY4pa5CCx20sV1ClOTZtAGngSOJtIOFXK599sBr5aIEFyH0K	7H4BoNMiiDMnxt1rD8Vb/ikJdhGMMQr0R4B	+L3nWU97eaVPTRKfWGDE8/eAgKzpGwrQQoDh+nzX1xoVQ8NAuH	+s4UcSeQ==

使用后可解锁更多功能！


* 主题头像

使用网络上的图像的URL后再本地可以显示，但是上传到github上不能显示，所以将图片都存入一个文件内，将头像地址直接指向本地文件的路径，等上传到github上的时候文件也会一并上传


	在blog/source下创建文件夹assets/blogImg目录，在该目录中存放图片6711866_014214_8838.jpg

在主题配置文件中使用该地址即可

	/assets/blogImg/6711866_014214_8838.jpg

默认会在source文件下寻找，即source就是根目录。

* 标签和分类
  标签和分类都是页面，所以要先创建页面：

  	hexo new page tags

  会在source文件夹下创建一个tags目录，里面有index.md文件，该文件就是标签页
>当点击博客中的标签选项时就会跳转到该页面，每个文章中的tags就会集中显示

打开后是：

	---
	title: tags
	date: 2019-10-31 14:10:12
	---
要添加一行：

	---
	title: tags
	date: 2019-10-31 14:10:12
	type: "tags"
	---
**保存并关闭文件**

在**博客文章的头部**新增属性：

	---
	title: Hexo 添加分类及标签
	date: 2019-04-24 15:40:24
	categories: hexo
	tags: hexo
	---



