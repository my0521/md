---
title: CentOS 安装Git
tags: Git,CentOS
date: 2019-05-06 20:00:48
categories: 
 - Git
---

在CentOS下安装Git

<!-- more -->

# CentOS 安装git

 - 安装 `yum install git -y`
 -  查看 `git --version`
 -  配置
``` verilog
  git config --global user.name "my"
  git config --global user.email "yeal166@163.com"  
```
 - 查看配置 `git config --list`

 - 设置ssh-key `ssh-keygen -t rsa -C "yeal166@163.com"`
   将`vim /root/.ssh/id_rsa.pub`内容添加到git服务器
 
# 本地仓库与远程仓库关联
 
## push 本地新项目

``` stylus
 echo "# md" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:my0521/md.git
git push -u origin main
```

## push本地已存在项目

``` stylus
git remote add origin git@github.com:my0521/md.git
git branch -M main
git push -u origin main
```

## 关联多个远程仓库
默认是关联在origin的远程库，如果需要关联在不同的远程库，需要为远程库指定名称
1. 关联github `git remote add github git@github.com:my/xxx.git`
2. 关联gitee `git remote add gitee git@gitee.com:my/xxx.git`

## 代码回滚

``` haskell
git checkout -- a.txt   # 丢弃某个文件，或者
git checkout -- .       # 丢弃全部
```
## 常用命令

 - `git branch`  查看本地所有分支
 - `git branch -r`查看远程所有分支
 - `git branch  xxx` 创建一个新的本地分支，不进行分支切换；
 - `git checkout  -b  xxx`  创建一个新的本地分支，切换
 - `git branch -a`查看本地和远程所有分支
 - `git checkout  -b  xxx   origin/xxx` 取远程分支   并   分化一个新的分支到本地
 - `git branch  -d | -D xxx` 删除xxx分支，D表示强制删除
 - `git branch -d  -r  xxx` 删除远程xxx分支
 - `git branch  -merged` 查看哪些分支合并入当前分支
 - `git branch  -no-merged`查看哪些分支未合并入当前分支
- `git fetch origin`更新远程库到本地
- `git push  origin  xxx`推送分支
- `git merged  origin/xxx`去远程分支合并到本地
- `git push  origin :mybranch`删除远程分支

## 错误处理
1.  `fatal: remote origin already exists.`
这说明本地库已经关联了一个origin的远程仓库
此时可以使用git remote -v查看远程库信息，
使用git remote rm origin删除已关联的远程仓库
之后再关联远程仓库



