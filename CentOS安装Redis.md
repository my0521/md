---
title: CentOS安装Redis
date: 2019-05-06 20:00:48
categories: 
 - Redis
tags:
 - Redis
 - CentOS
---

在CentOS下安装Redis

<!-- more -->

redis下载地址：[http://download.redis.io/releases/](http://download.redis.io/releases/)

## 安装

1. 下载
``` groovy
wget http://download.redis.io/releases/redis-6.0.5.tar.gz
```
2. 解压

``` stylus
tar -zxvf redis-6.0.5.tar.gz -C /usr/local/src/
```

3. 安装 `cd /usr/local/src/redis-6.0.5/` 
``` vim
 make 
 cd src/
 make install PREFIX=/usr/local/redis
cp /usr/local/src/redis-6.0.5/redis.conf /usr/local/redis/redis.conf
```
## 配置
``` nginx
#bind 127.0.0.1 注释掉允许外网访问
daemonize yes #允许在后台启动
protected-mode no #去掉登录密码
```

## 启动

``` vbscript
 ./redis-server redis.conf
```
- 查看进程 `ps -aux | grep redis`
- 关闭redis `kill  12158`

## 开机启动

``` groovy
mkdir /etc/redis
cp /usr/local/redis/redis.conf /etc/redis/6379.conf
cp /usr/local/src/redis-6.0.5/utils/redis_init_script /etc/init.d/redisd
chkconfig redisd on
```

