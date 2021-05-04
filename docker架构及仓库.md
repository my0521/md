---
title: docker架构及仓库
date: 2020-07-01 20:00:48
categories: 
- docker
tags:
- docker
- CentOS
---

docker架构及仓库

<!-- more -->

## 官网
英文官网[https://docs.docker.com/get-started/][1]
中文官网[www.docker-cn.com][2]

## 仓库
[https://hub.docker.com/][3]

### 加速方式
1. 请求时指定镜像地址
``` stylus
docker pull registry.docker-cn.com/library/ubuntu:16.04
```
2. 启动docker守护进程时，添加`--registry-mirror`参数
3. 修改配置文件/etc/docker/daemon.json
``` json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

## 架构
Docker 采用的是 Client/Server 架构。客户端向服务器发送请求，服务器负责构建、运行和分发容器。客户端和服务器可以运行在同一个 Host 上，客户端也可以通过 socket 或 REST API 与远程的服务器通信
1. client
- 通过 docker 我们可以方便地在 Host 上构建和运行容器
2. docker服务器 （docker 守护进程）
- Docker daemon 是服务器组件，以 Linux 后台服务的方式运行
- Docker daemon 运行在 Docker host 上，负责创建、运行、监控容器，构建、存储镜像
- 默认配置下，Docker daemon 只能响应来自本地 Host 的客户端请求。如果要允许远程客户端请求，需要在配置文件中打开 TCP 监听
``` stylus
1. 编辑配置文件 /etc/systemd/system/multi-user.target.wants/docker.service，在环境变量 ExecStart 后面添加 -H tcp://0.0.0.0，允许来自任意 IP 的客户端连接。
如果使用的是其他操作系统，配置文件的位置可能会不一样。
2. 重启 Docker daemon
3. 服务器 IP 为 192.168.56.102，客户端在命令行里加上 -H 参数，即可与远程服务器通信。
info 子命令用于查看 Docker 服务器的信息
```
3. docker镜像
``` undefined
镜像有多种生成方法：
1. 可以从无到有开始创建镜像
2. 也可以下载并使用别人创建好的现成的镜像
3. 还可以在现有镜像上创建新的镜像
```
4. docker容器 镜像运行的实例
``` stata
Docker 容器就是 Docker 镜像的运行实例
用户可以通过 CLI（docker）或是 API 启动、停止、移动或删除容器。可以这么认为，对于应用软件，镜像是软件生命周期的构建和打包阶段，而容器则是启动和运行阶段
```
5. 仓库 存放镜像的仓库，包含共有和私有
- 私有
- 公有
docker pull 命令可以从 Registry 下载镜像。
docker run 命令则是先下载镜像（如果本地没有），然后再启动容器

## 运行过程
1. 当client 执行命令docker run nginx时，client发送socket消息给docker守护进程，
2. docker守护进程先在本地看下有没有这个镜像存在，如果不存在就去远程仓库下载，然后保存到本地；
3. 然后通过 container run命令把这个镜像做成一个容器然后运行起来


  [1]: https://docs.docker.com/get-started/
  [2]: www.docker-cn.com
  [3]: https://hub.docker.com/
