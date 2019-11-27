---
title: 完全卸载mysql
date: 2019-10-31 10:30:00
tags:
- mysql
categories:
- 数据库
---

1. 先停止mysql服务
2. 进入控制面板卸载mysql
3. 删除mysql文件，一般在C:\Program Files (x86)下有个mysql文件夹，直接删除
4. 在运行中输入regedit打开注册表，删除**HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Eventlog\Application\MySQL文件夹**和**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application\MySQL的文件夹**
>ControlSet001和CurrentControlSet的区别若后者没有mysql可以不删




