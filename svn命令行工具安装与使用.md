---
title: svn命令行工具安装与使用 
date: 2018-01-01 20:00:48
categories: 
- svn
tags:
- svn
---

记录windows下安装svn命令行工具过程

<!-- more -->

## 安装
1. 下载32位 [https://sourceforge.net/projects/win32svn/][1]
2. 安装
启动下载exe直接按照windows安装一路next即可，可以自定义安装路径

## 命令
1. 查看svn支持的命令
``` sql
svn help
```
2. 查看某个命令的语法
``` stata
svn help ci 
```

### 常用命令格式及示例
1. 导入项目
``` puppet
svn import http://svn.chinasvn.com:82/pthread --message "Start project"
```
2. 导出项目
``` groovy
svn checkout http://svn.chinasvn.com:82/pthread
```
3. 采用 export 的方式来导出一份“干净”的项目
``` objectivec
svn export http://svn.chinasvn.com:82/pthread pthread
```
4. 为失败的事务清场
``` nginx
svn cleanup
```
5. 在本地进行代码修改，检查修改状态
``` fortran
svn status -v
svn diff
```
6. 更新(update)服务器数据到本地
``` stata
svn update directory
svn update file
```
7. 增加(add)本地数据到服务器
``` vim
svn add file.c
svn add dir
```
8. 对文件进行改名和删除
``` stylus
svn mv b.c bb.c
svn rm d.c
```

9. 提交(commit)本地文档到服务器
``` stata
svn commit
svn ci
svn ci -m "commit"
```
10. 查看日志
``` livecodeserver
svn log directory
svn log file
```







  [1]: https://sourceforge.net/projects/win32svn/
