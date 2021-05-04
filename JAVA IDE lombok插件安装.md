---
title: JAVA IDE lombok插件安装
date: 2019-05-06 20:00:48
categories: 
 - lombok
tags:
 - Java
 - lombok
---

IDE中安装lombok插件方法

<!-- more -->

## STS 安装
1. 进入mvn中依赖lombok目录
``` mel
%MAVEN_HOME%\org\projectlombok\lombok\1.18.6
```
2. 进入cmd环境，切换到lombok目录，运行`lombok-1.18.6.jar`
``` nginx
java -jar lombok-1.18.6.jar
```
3. 找到STS的路径，安装lombok
4. 编辑STS配置文件
- 第3步成功后，在STS目录会有一个lombok.jar
- 在`SpringToolSuite4.ini`最后一行添加`-vmargs -javaagent:lombok.jar`



