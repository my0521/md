---
title: RocketMQ可视化工具
date: 2019-05-06 20:00:48
categories: 
 - MQ
tags:
 - RocketMQ
 - Java
---
RocketMQ有一个对其扩展的开源项目incubator-rocketmq-externals，这个项目中有一个子模块叫`rocketmq-console`，这个便是管理控制台项目了

<!-- more -->

源码地址： [https://github.com/apache/rocketmq-externals][1]
版本下载   `https://github.com/apache/rocketmq-externals.git`

## 打包rocketmq-console
1. 进入到rocketmq-console子目录
2. mvn package
3. 运行
``` brainfuck
 # 默认链接本地
 java -jar rocketmq-console-ng-1.0.0.jar
 ## 制定端口 与 namesrvAddr地址
 java -jar rocketmq-console-ng-1.0.0.jar --server.port=12345 --rocketmq.config.namesrvAddr=121.36.64.197:9876
 #连接集群
 java -jar rocketmq-console-ng-1.0.0.jar --server.port=12581 --rocketmq.config.namesrvAddr=121.36.64.197:9876;121.36.64.198:9876
```
4. 在linux下后台运行脚本，将启动jar包的命令写入bash脚本 `nohup ./start.sh &`
5. 访问 [http://192.168.0.100:8080][2]


## 报错记录
1. 源码格式报错 
- 报错 `DescriptionResourcePathLocationType Plugin execution not covered by lifecycle configuration: org.apache.maven.plugin`
- 解决方法： 将`<plugins>...</plugins>`改为`<pluginManagement><plugins>...</plugins></pluginManagement>`
2. mvn package报错
``` sql
[ERROR] Failed to execute goal on project rocketmq-console-ng: Could not resolve dependencies for project org.apache:rocketmq-console-ng:jar:1.0.0: The following artifacts could not be resolved: org.apache.rocketmq:rocketmq-tools:jar:4.4.0-SNAPSHOT, org.apache.rocketmq:rocketmq-namesrv:jar:4.4.0-SNA
PSHOT, org.apache.rocketmq:rocketmq-broker:jar:4.4.0-SNAPSHOT: Failure to find org.apache.rocketmq:rocketmq-tools:jar:4.4.0-SNAPSHOT in http://maven.aliyun.com/nexus/content/groups/public/ was cached in the local repository, resolution will not be reattempted until the update interval of alimaven ha
s elapsed or updates are forced -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
```
- 解决方法： 将项目 rocketmq-console中POM.xml文件中`4.4.0-SNAPSHOT`修改未`4.4.0`


  [1]: https://github.com/apache/rocketmq-externals
  [2]: http://192.168.0.100:8080

