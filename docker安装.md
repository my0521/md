---
title: docker安装
date: 2020-07-01 20:00:48
categories: 
- docker
tags:
- docker
- CentOS
---

记录学习CentOS下docker的安装过程
<!-- more -->

## 安装docker
卸载

``` tex
yum remove -y docker \
              docker-client \
              docker-client-latest \
              docker-common \
              docker-latest \
              docker-latest-logrotate \
              docker-logrotate \
              docker-engine
```

### 查看内核版本 
docker官方说至少3.8以上，建议3.10以上
``` bash
uname -a
```

### 把yum包更新到最新 
``` sql
yum update
```

### 安装需要的软件包
yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的
``` sql
yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 设置yum源
``` vim
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

## 所有docker版本
``` stata
yum list docker-ce --showduplicates | sort -r
```

## 安装docker
``` vim
yum install docker-ce-18.06.3.ce
#安装最新版
yum install -y docker-ce  
```

## 启动docker
1. 启动 `systemctl start docker`
2. 添加开机启动项 `systemctl enable docker`

## 验证 
1. `docker version`

## docker常用命令
1. 查看本地存在的docker镜像 
``` nginx
docker images
------------------------------------------------------
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

```
2. 查看正在运行的所有容器
``` vim
docker ps -a
------------------------------------------------------
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
e93ff5fc274d        hello-world         "/hello"            8 minutes ago       Exited (0) 8 minutes ago                       modest_panini
9b0f9cb538fb        hello-world         "/hello"            9 minutes ago       Exited (0) 9 minutes ago  
```
3. 查看最近创建的容器
``` stata
docker ps -l
------------------------------------------------------
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
e93ff5fc274d        hello-world         "/hello"            8 minutes ago       Exited (0) 8 minutes ago  
```
4. 查看容器运行的日志
``` bash
docker logs e93ff5fc274d  
```
5. 停止运行容器
``` sql
docker stop e93ff5fc274d  
```
6. 删除容器
``` stata
docker rm e93ff5fc274d  
```
7. 查看容器端口
``` bash
docker port e93ff5fc274d  
```
8. 查看容器内部运行的进程
``` scss
docker top e93ff5fc274d 
```
9. 查看容器的底层信息
``` gradle
docker inspect e93ff5fc274d 
```
10. 进入正在运行的容器执行命令
``` mel
docker exec e93ff5fc274d ls /usr/share/nginx
```
11. docker与主机文件交换
``` vim
touch /cp.txt && \
docker cp /cp.txt c48d842f376a:/ && \
docker exec c48d842f376a ls /
```



 


## docker基本使用
1. 从docker仓库搜索ubuntu镜像
``` stata
docker search ubuntu
```
2. 下载ubuntu镜像
``` nginx
docker pull ubuntu
#在下载镜像后加":版本号"下载指定版本镜像，不指定则默认最新的":latest"
docker pull nginx:1.16
```
3. 运行ubuntu交互式环境
- docker run -it ubuntu /bin/bash
- 退出 CTRL+D
4. 以后台模式启动容器
``` stata
docker run -d ubuntu /bin/sh -c "while true; do echo hello world;sleep 1; done"
```

## 创建镜像
1. 使用 `Dockerfile` 指令来创建一个新的镜像

## 安装CentOS镜像
1. 下载centos7镜像
``` profile
docker pull daocloud.io/centos:latest
```
2. 运行centos7容器
``` brainfuck
docker run -dit --privileged --name=centos7 daocloud.io/centos:latest /usr/sbin/init
```
3. 进入centos容器交互界面
``` mel
docker exec -it centos7 /bin/bash
```
4. 按CentOS方式安装MySql









