---
title: CentOS安装zookeeper
date: 2019-05-06 20:00:48
categories: 
 - zookeeper
tags:
 - zookeeper
 - CentOS
---
CentOS7下安装zookeeper，在命令行情况下如何操作Zookeper的客户端和服务端，并且使用简单的命令完成对Zookeeper的一些操作

<!-- more -->

下载地址：[http://mirror.bit.edu.cn/apache/zookeeper/][1]

## 下载
``` elixir
wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.14/zookeeper-3.4.14.tar.gz
```

## 安装
1. 解压
``` stylus
tar -zxvf zookeeper-3.4.14.tar.gz -C /usr/local/
```
2. 重命名
``` stata
cd /usr/local/
mv zookeeper-3.4.14/ zookeeper/
cd zookeeper/
```
3. 配置
- `cd conf/`
- `cp zoo_sample.cfg zoo.cfg`
- 修改
``` vala
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/tmp/zookeeper/data
dataLogDir=/tmp/zookeeper/logs
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
server.1=192.168.0.186:2888:38888
```
4. 创建数据和日志文件夹
- `mkdir /tmp/zookeeper/data`
- `mkdir /tmp/zookeeper/logs`
5. 配置ZOOKEEPER_HOME环境变量
- `vim /etc/profile`
``` bash
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=${ZOOKEEPER_HOME}:$PATH
```
- `source /etc/profile`

## 启动
1. 启动 `./zkServer.sh start`
``` stylus
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```
2. 查看状态 `./zkServer.sh status`
``` groovy
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: standalone
```
3. jps查看QuorumPeerMain进程
``` undefined
18224 QuorumPeerMain
18241 Jps
```

## 命令行

### 启动客户端
1. 连接zookeeper服务端，启动成功后，进入zookeeper的命令行环境
``` stylus
./zkCli.sh 
```
WATCHER::

WatchedEvent state:SyncConnected type:None path:null
- 连接不同的主机
``` stylus
./zkCli.sh -server ip:port
```
2. 在客户端命令行环境使用help查看客户端支持的操作
``` dos
[zk: localhost:2181(CONNECTED) 1] help
ZooKeeper -server host:port cmd args
	stat path [watch]
	set path data [version]
	ls path [watch]
	delquota [-n|-b] path
	ls2 path [watch]
	setAcl path acl
	setquota -n|-b val path
	history 
	redo cmdno
	printwatches on|off
	delete path [version]
	sync path
	listquota path
	rmr path
	get path [watch]
	create [-s] [-e] path data acl
	addauth scheme auth
	quit 
	getAcl path
	close 
	connect host:port
```

### 客户端命令
1. 创建zookeeper节点
使用create命令，可以创建一个Zookeeper节点， 如

　　create [-s] [-e] path data acl

　　其中，-s或-e分别指定节点特性，顺序或临时节点，若不指定，则表示持久节点；acl用来进行权限控制。
- 创建顺序节点
``` sql
create -s /zk-test 123
```
- 创建临时节点
``` sql
create -e /zk-temp 123 
```
- 创建永久节点
``` nginx
create /zk-permanent 123
```
2. 读取节点
``` scss
ls path [watch]
get path [watch]
ls2 path [watch]
```
3. 更新节点
``` sql
set path data [version]
```
4. 删除节点
``` sql
delete path [version]
```

## 报错
1. Could not find or load main class org.apache.zookeeper.server.quorum.QuorumPeerMain
解决方案：在网站上下载以zookeeper开头的压缩包，笔者一开始使用最新版与stable版本都无法正常启动。

















  [1]: http://mirror.bit.edu.cn/apache/zookeeper/
