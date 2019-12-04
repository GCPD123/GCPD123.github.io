---
title: iterm2+oh-my-zsh替代mac终端
date: 2019-12-02 21:09:34
tags: 终端
categories: 工具

---

### mac自带的终端

终端其实是一个与操作系统打交道的命令行界面，其中运行的程序叫shell（壳），之所以这么叫是因为操作系统就好比核心，我们不能直接与操作系统打交道，必须与shell交流通过它来操作系统。mac系统默认使用bash作为终端，通过

```
echo $SHELL
```

壳查看当前系统默认的shell，通过

```
cat /etc/shells
```

可查看系统所有存在的shell，所有的shell都存在于/etc/shell文件中，若要切换默认shell，可使用

```
chsh -s /bin/zsh 或者 chsh -s /bin/bash	
```

### 下载安装iterm2

傻瓜式安装后即可使用，就是一个软件，和mac自带的终端一模一样，也是命令行，此时打开iterm2后默认的系统shell还是bash，可以使用以上命令查看，我们应该切换到zsh的shell，切换后界面和bash也是一样，我们还需要美化它，需要下载oh-my-zsh，直接在iterm2中输入

```

# curl 安装方式

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

```

即可自动安装安装好后如图：

![](2411388-654b2a938885224c.png)



### 修改iterm2到最佳外观

![屏幕快照 2019-12-02 下午9.40.55](1.png)

设置为键盘左上角可显示/隐藏iterm2很方便

![屏幕快照 2019-12-02 下午9.44.26](2.png)

该设置可让外观好看，如下

![屏幕快照 2019-12-02 下午9.45.46](3.png)

若不喜欢打开后最上面显示上次的登陆信息，在～目录下可使用：

```
touch .hushlogin
```

