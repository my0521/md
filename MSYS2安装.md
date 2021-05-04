---
title: MSYS2安装
date: 2020-07-01 20:00:48
categories: 
- MSYS2
tags:
- MSYS2
---

MSYS2 下载安装及基本配置
<!-- more -->

官网 [https://www.msys2.org/][1]

## 简介
1. MSYS2
MSYS2 [1]  是MSYS的一个升级版,准确的说是集成了pacman和Mingw-w64的Cygwin升级版, 提供了bash shell等linux环境、版本控制软件（git/hg）和MinGW-w64 工具链。与MSYS最大的区别是移植了 Arch Linux的软件包管理系统 Pacman(其实是与Cygwin的区别)
2. 特色
- 安装方便
- 自带 pacman 管理，可以使用 pkgtool 来 makepkg
- 较快的源速度（可以修改源地址）
- 自带软件和库较全而且比较新
- 使用mingw-w64工具链,可以编译32位或64位代码（需要自行安装）
- 中文支持好,可以直接输入和浏览中文目录

## 安装
1. 下载
从官网[https://www.msys2.org/][2]的`Installation`模块下载最新安装包
2. 启动msys2
3. 更新环境
``` nginx
pacman -Syu
```

## 常用pacman命令
1. pacman -Q 查看已安装的软件包
2. pacman -S -g查看软件组
3. pacman -Q -g base-devel查看软件组包含的软件
4. pacman -Q -l vim查询软件包的内容
5. pacman -Q -s nettle查询软件所在的包
6. pacman -h ;pacman -S -h 查看工具帮助
建议通过安装软件组来安装工具链
7. pacman -S mingw-w64-x86_64-toolchain
8. pacman -S mingw-w64-i686-toolchain
9. pacman -S base-devel
10. pacman -S vim 
11. pacman -S package-name 安装
12. pacman -R package-name 删除
13. pacman -Sl 列出可用的包，包含未安装的包
14. pacman -Sl | grep gcc 查看可用的gcc安装包

## 运行环境说明
1. MSYS2安装根目录有三个执行脚本，分别是 msys2_shell.bat、mingw32_shell.bat 和 mingw64_shell.bat，查看内容可以看到其中只有一行区别，即是设定 MSYSTEM 变量。这个变量在 /etc/profile 中会用到
2. 三个 .bat 的区别就是 PATH 的设置，mingw32_shell.bat 优先使用 msys64/mingw32 下的工具，mingw64_shell.bat 优先使用 msys64/mingw64 下的工具，而 msys2_shell.bat 两个都不使用，只用自身 msys 的工具。这么做的好处是当需要编译 32bit Target 的项目时使用 mingw32_shell.bat，64 bit 使用 mingw64_shell.bat，各套工具互不干扰

## 安装make
1. 查找make相关的make工具
``` vim
pacman -Sl |grep make
```
2. 很多包里都有make工具，安装 MSYS2 的make
``` go
pacman -S make
```

## 安装 Git
1. 搜索 git
``` gradle
pacman -Sl |grep git
```
2. 安装 MSYS2 的 git
``` nginx
pacman -S git
```

## 镜像
1. 本机安装到准备更新镜像时没发现镜像已是最新，猜测pacman -Syu之后就已更新，可以将清华镜像放在最前面，具体操作可参考
[https://mirror.tuna.tsinghua.edu.cn/help/msys2/][3]


  [1]: https://www.msys2.org/
  [2]: https://www.msys2.org/
  [3]: https://mirror.tuna.tsinghua.edu.cn/help/msys2/
