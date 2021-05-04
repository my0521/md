---
title: Nodejs
date: 2020-05-03 18:49:48
categories: 
- Nodejs
tags:
- Nodejs
- CentOS
---

记录CentOS下源码安装最新Nodejs
<!-- more -->

## 安装nodejs
1. 删除nodejs
``` vim
yum remove nodejs
```
2. 更新node.js yum存储库 setup_12.x为安装的版本
``` vim
yum clean all && yum makecache fast
yum install -y gcc-c++ make
curl -sL https://rpm.nodesource.com/setup_12.x | sudo -E bash
```
3. 安装nodejs
``` stylus
yum install nodejs
```
4. 验证
``` stylus
node -v
```
v12.18.2



