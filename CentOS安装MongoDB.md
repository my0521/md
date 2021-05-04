---
title: CentOS安装MongoDB
date: 2020-07-01 20:00:48
categories: 
- MongoDB
tags:
- MongoDB
---

记录CentOS安装MongoDB及配置全过程

<!-- more -->

## 下载
官网下载地址：[https://www.mongodb.com/download-center/community/releases/archive][1]

## 安装
1. wget下载
``` groovy
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-4.2.7.tgz
```
2. 解压到`/usr/local/`
``` stylus
tar -zxvf mongodb-linux-x86_64-rhel70-4.2.7.tgz -C /usr/local/
```
3. 进入解压目录
``` stata
cd /usr/local/
mv mongodb-linux-x86_64-rhel70-4.2.7/ mongodb/
cd mongodb/
```
4. 创建文件目录
``` stata
mkdir db
mkdir log
touch log/mongo.log
```
5. 新建 `vim mongo.conf`
``` vim
port=27017
bind_ip=0.0.0.0
dbpath=/usr/local/mongodb/db
logpath=/usr/local/mongodb/log/mongo.log
logappend=true
```

## 使用
1. 启动
``` stylus
nohup bin/mongod -f mongo.conf &
```
2. 连接
``` undefined
bin/mongo 127.0.0.1:27017
```
3. 添加账户
``` groovy
use admin
db.createUser({ user: "root", pwd: "root", roles: [{ role: "userAdminAnyDatabase", db: "admin" }] })
```
4. `mongo.conf`新增配置
``` ini
auth=true
```
5. 重启后登陆
``` undefined
nohup bin/mongod -f mongo.conf &
bin/mongo 127.0.0.1:27017
```
6. 登陆
``` stylus
use admin
db.auth("root", "root")
```
7. 查看所有数据库
``` stylus
 show databases
```


  [1]: https://www.mongodb.com/download-center/community/releases/archive
