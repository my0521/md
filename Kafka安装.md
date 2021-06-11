---
title: Kafka安装
date: 2021-05-22 20:00:48
categories: 
-  Kafka
tags:
-  Kafka
---

Kafka 在CentOS8下安装
<!-- more -->

# 前言
Kafka 的使用依赖zookpeer,先安装Kafka，然后查看Kafka的zookpeer版本，下载对应的版本安装


# 下载
- kafka_2.13-2.8.0.tgz
``` x86asm
wget https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.8.0/kafka_2.13-2.8.0.tgz
```
- apache-zookeeper-3.5.9-bin.tar.gz 
``` javascript
wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.5.9/apache-zookeeper-3.5.9-bin.tar.gz
```
都解压到/usr/local/


# 环境变量设置
-  vim /etc/profile
``` javascript
export JAVA_HOME=/usr/local/jdk1.8.0_291
export JRE_HOME=$JAVA_HOME/jre
export CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/
export KAFKA_HOME=/usr/local/kafka_2.13-2.8.0
export PATH=$PATH:$KAFKA_HOME/bin:$JAVA_HOME/bin:$JRE_HOME/bin
```
-  source /etc/profile

# 安装Kafka
 1. 解压到安装目录
``` lsl
 tar -zxvf /tmp/kafka_2.13-2.8.0.tgz -C /usr/local/
```
 2. 前端启动    -daemon 为启动守护进程
``` lsl
cd /usr/local/kafka_2.13-2.8.0 & bin/kafka-server-start.sh -daemon config/server.properties
```  
3. 后台启动
  ``` lsl
cd /usr/local/kafka_2.13-2.8.0 & nohup bin/kafka-server-start.sh config/server.properties >> /dev/null &
```

# 安装zookpeer
1. 解压到安装目录
``` stylus
tar -zxvf /tmp/apache-zookeeper-3.5.9-bin.tar.gz -C /usr/local/
```
2. 备份配置文件 
``` javascript
cp /usr/local/apache-zookeeper-3.5.9-bin/conf/zoo_sample.cfg /usr/local/apache-zookeeper-3.5.9-bin/conf/zoo.cfg
```
3. 启动 
``` javascript
/usr/local/apache-zookeeper-3.5.9-bin/zkServer.sh start
```
4. 停止
``` javascript
/usr/local/apache-zookeeper-3.5.9-bin/zkServer.sh  stop
```

# 测试
1. 创建topic
``` jboss-cli
cd /usr/local/kafka_2.13-2.8.0/ & bin/kafka-topics.sh --create --replication-factor 1 --partitions 1 --topic test --zookeeper localhost:2181
```
2. 创建生产者
``` stata
 cd /usr/local/kafka_2.13-2.8.0/ & bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```
       启动成功会阻塞 需要另外重新打开 可以在控制台输出生产者数据 消费者能及时消费
3. 创建消费者
``` stata
 cd /usr/local/kafka_2.13-2.8.0/ & bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```
    启动后可以实时消费生产者生产的消息

# topic的操作

 - 查看列表
``` css
kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
```

 - 添加
``` dsconfig
kafka-topics.sh --create --replication-factor 1 --partitions 1 --topic test --zookeeper localhost:2181
```

 - 查看某个topic
``` dsconfig
kafka-topics.sh --zookeeper localhost:2181 --describe --topic test
```

 - 删除topic（分两步）
1. `kafka-topics.sh  --delete --zookeeper localhost:2181 --topic test`
2. `cd  /tmp/kafka-logs  & rm -rf test-0`





