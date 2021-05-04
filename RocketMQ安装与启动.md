---
title: RocketMQ安装与启动
date: 2019-05-06 20:00:48
categories: 
 - MQ
tags:
 - RocketMQ
 - centOS
---

记录RocketMQ安装及爬坑的一些问题
<!-- more -->

RocketMQ 官网： [http://rocketmq.apache.org][1]

## rocketM依赖环境
- JAVA JDK1.8

## 下载
1. 下载
``` groovy
cd /tmp/
wget http://mirrors.tuna.tsinghua.edu.cn/apache/rocketmq/4.7.1/rocketmq-all-4.7.1-bin-release.zip
```

2. 解压到安装目录
``` sql
yum install unzip
unzip /tmp/rocketmq-all-4.7.1-bin-release.zip -d /usr/local/
mv rocketmq-all-4.7.1-bin-release/ rocketmq/
```

## 配置
1. 配置JAVA环境变量
rocketMQ服务启动有2个基本脚本runserver.sh与runbroker.sh，默认的启动一般会报错，一般是JAVA环境变量与内存溢出错误，即使系统环境变量正常，在此处脚本中还是需要配置JAVA_HOME,本机在一个1G内存的服务器上搭建，使用配置如下;
- runserver.sh 修改之处
``` bash
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=$HOME/java/jdk1.8.0_251
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java
#[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"
DIR_SIZE_IN_MB=60
JAVA_OPT="${JAVA_OPT} -server -Xms32m -Xmx64m -Xmn32m -XX:MetaspaceSize=32m -XX:MaxMetaspaceSize=64m"
JAVA_OPT="${JAVA_OPT} -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=30m"
```
- runbroker.sh 修改之处
``` bash
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=$HOME/java/jdk1.8.0_251
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java
#[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"
DIR_SIZE_IN_MB=60
JAVA_OPT="${JAVA_OPT} -server -Xms32m -Xmx64m -Xmn32m"
JAVA_OPT="${JAVA_OPT} -XX:+UseG1GC -XX:G1HeapRegionSize=32m -XX:G1ReservePercent=25 -XX:InitiatingHeapOccupancyPercent=30 -XX:SoftRefLRUPolicyMSPerMB=0"
JAVA_OPT="${JAVA_OPT} -XX:MaxDirectMemorySize=32m"
```
- vim /etc/profile添加
``` nginx
 export NAMESRV_ADDR=127.0.0.1:9876
```
- tools.sh 修改
``` stata
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=$HOME/java/jdk1.8.0_251
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java
#[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"
JAVA_OPT="${JAVA_OPT} -server -Xms32m -Xmx32m -Xmn64m -XX:MetaspaceSize=32m -XX:MaxMetaspaceSize=32m"
```

## 启动
1. 进入rocketmq目录  `cd /usr/local/rocketmq`
2. 启动mqnamesrv   `nohup sh bin/mqnamesrv &`
``` autohotkey
执行命令后,重新开启一个终端，查看`nohup.out`出现`The Name Server boot success. serializeType=JSON` ，服务启动成功
```
3. 启动broker `nohup sh bin/mqbroker -n localhost:9876 &`
``` vbscript
The broker[hecs-x-medium-2-linux-20200606090800, 192.168.0.186:10911] boot success. serializeType=JSON and name server is localhost:9876
```
4. 查看启动后进程  `jps`
``` log
15556 NamesrvStartup
15740 Jps
15583 BrokerStartup
```

## 测试
1. 生产消息
``` stylus
./bin/tools org.apache.rocketmq.example.quickstart.Producer
```
2. 消费消息
``` stylus
./bin/tools org.apache.rocketmq.example.quickstart.Consumer
```
- 说明
如果服务正常，会出现发送消息与接收消息的打印日志，如下：
 1. `SendResult [sendStatus=SEND_OK, msgId=C0A800BA47FC6D6F6E285120A91D03E7, offsetMsgId=792440C500002A9F0000000000092BAB, messageQueue=MessageQueue [topic=TopicTest, brokerName=broker-a, queueId=2], queueOffset=740]` 
 2. `ConsumeMessageThread_1 Receive New Messages: [MessageExt [brokerName=broker-a, queueId=0, storeSize=203, queueOffset=488, sysFlag=0, bornTimestamp=1594893231158, bornHost=/121.36.64.197:41676, storeTimestamp=1594893231159, storeHost=/121.36.64.197:10911, msgId=792440C500002A9F0000000000060CC9, commitLogOffset=396489, bodyCRC=684865321, reconsumeTimes=0, preparedTransactionOffset=0, toString()=Message{topic='TopicTest', flag=0, properties={MIN_OFFSET=0, MAX_OFFSET=491, CONSUME_START_TIME=1594893303537, UNIQ_KEY=C0A800BA476D6D6F6E285116883603DF, CLUSTER=DefaultCluster, WAIT=true, TAGS=TagA}, body=[72, 101, 108, 108, 111, 32, 82, 111, 99, 107, 101, 116, 77, 81, 32, 57, 57, 49], transactionId='null'}]]` 

## 错误
1. Address already in use
服务已经启动或者端口被占用

2. *JAVA_HOME/bin/java: No such file or directory
无法找到java命令路径，这里的路径指向JAVA_HOME/bin/java，将脚本中的JAVA_HOME变量修改为本地JDK根目录即可。

3. [NettyClientSelector_1] INFO  RocketmqRemoting - closeChannel: close the connection to remote address[] result: true
这个问题是无法连接到服务造成的，原因可能是系统防火墙拦截、系统的端口未开放（注意有2个端口，一个namesrv：10911，一个broker：9876，当时因为10911端口未开放，瞎折腾半天）


  [1]: http://rocketmq.apache.org
