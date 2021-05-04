---
title: gitee版本仓库管理 
date: 2020-07-01 20:00:48
categories: 
- git
tags:
- git
---

Git的安装及常用操作

<!-- more -->

## 安装git
``` sql
yum install git
```
## 配置用户信息

``` git
git config --global user.name "my"
git config --global user.email "yeal166@163.com"
```
### 查看配置信息

``` git
git config --list
```
  user.name=my
  user.email=yeal166@163.com
## 设置sshkey 

 1. 默认路径/root/.ssh/id_rsa.pub.
`ssh-keygen -t rsa -C "yeal166@163.com"`


2. 登录gitee，在个人信息-设置-ssh公钥中添加
将id_rsa.pub文件的内容复制到公钥里面，点击确定
  `vim /root/.ssh/id_rsa.pub`

## 本地仓库与远程仓库关联
本地新建的项目需要关联到远程版本仓库 

``` git
git remote add origin git@gitee.com:my/xxx.git
```
 
之后本地仓库就与远程仓库完全同步。

## 本地仓库提交到远程仓库
1. `mkdir abc`
2. `cd abc`
3. `git init`
4. `git add .`
5. `git commit -m "log"`
6. `git remote add origin git@gitee.com:my/xxx.git`
7. `git pull --rebase origin master`
8. `git push -u origin master`

### 与远程仓库同步报错 

 1. `fatal: remote origin already exists.`
 
 - 这说明本地库已经关联了一个`origin`的远程仓库
 - 此时可以使用`git remote -v`查看远程库信息，
 - 使用`git remote rm origin`删除已关联的远程仓库
 - 之后再关联远程仓库

## 关联多个远程仓库

 1. 默认是关联在origin的远程库，如果需要关联在不同的远程库，需要为远程库指定名称
 
 -  关联github `git remote add github git@github.com:my/xxx.git`
 -  关联gitee  `git remote add gitee git@gitee.com:my/xxx.git`

## 代码回滚
``` sql
git checkout -- a.txt   # 丢弃某个文件，或者
git checkout -- .       # 丢弃全部
```


 

