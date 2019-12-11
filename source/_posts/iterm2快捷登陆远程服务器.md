---
title: iterm2快捷登陆远程服务器
date: 2019-12-11 15:45:11
tags: 终端
categories: 工具
---

### 正常使用ssh登陆服务器

```
ssh -p port user@host

user@host's password:
```

整个ssh登陆过程：

1. 用户向远程主机发送登陆请求：ssh user@host
2. 远程主机收到用户请求，**把自己的公钥发送给用户**
3. 用户使用这个公钥，**将登陆密码加密**，发送回远程主机
4. 远程主机使用自己的私钥，解密登陆密码，如果密码正确，就同意用户登陆

在linux上，如果是第一次登陆远程主机，会出现如下：



```
$ ssh user@host 
The authenticity of host 'host (12.18.429.21)' can't be established. 
RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d. 
Are you sure you want to continue connecting (yes/no)?
```

这段话的意思是：无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗

>很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。



所谓"公钥指纹"，是指公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。上例中是98:2e:d7:e0🇩🇪9f:ac:67:28:c2:42:2d:37:16:58:4d，再进行比较，就容易多了。

```
Are you sure you want to continue connecting (yes/no)? yes
```

系统会出现一句提示，表示host主机已经得到认可。

```
Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.
```

然后输入密码，如果正确就可以登陆

当远程主机的公钥被接受以后，它就会被保存在文件  $HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。
每个SSH用户都有自己的known_hosts文件，分别在自己的$HOME目录下，此外操作系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。

一句话：第一次连接成功后，远程主机的公钥就被保存在了本机，下次再连接就可以直接输入密码了



### 使用iterm2 Profiles快捷登陆ssh

找一个目录创建一个普通文件，在里面写入：

![](1.png)

打开iterm2 -> preferences -> Profiles
点击下面“+”号，新建一个profile。
选择Command 在输入框中输入
expect+刚才建的文件路径

![](2.png)

![](3.png)

直接点击就可以登陆