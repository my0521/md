---
title: CentOS更新yum源
date: 2018-01-01 20:00:48
categories: 
- CentOS
tags:
- CentOS
---

CentOS更新自己的yum源

<!-- more -->
cat /etc/yum.repos.d/CentOS-Base.repo

1、备份
cd /etc/yum.repos.d/
mkdir bak
mv *.repo ./bak/

2、下载新的CentOS-Base.repo 到/etc/yum.repos.d/
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
cat /etc/yum.repos.d/CentOS-Base.repo

3、之后运行yum makecache生成缓存
yum makecache