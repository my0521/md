---
title: CentOS7安装JDK8
date: 2020-05-04 18:49:48
categories:
- JDK
tags:
- JDK
- CentOS
---

在CentOS下安装JDK8，以及配置环境变量

<!-- more -->

## 下载JDK

JDK8  [https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html][1]

## 安装
- 将压缩包解压到指定安装目录

``` stylus
mkdir -p /usr/local/java
tar -zxvf /tmp/jdk-8u251-linux-x64.tar.gz -C /usr/local/java/
```
- 进入安装目录

``` gradle
cd /usr/local/java/
```
## 配置环境变量
- 设置环境变量

``` objectivec

export JAVA_HOME=/usr/local/java/jdk1.8.0_251
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

```
## 验证JDK

``` lisp
java -version
```
	java version "1.8.0_251"
	Java(TM) SE Runtime Environment (build 1.8.0_251-b08)
	Java HotSpot(TM) 64-Bit Server VM (build 25.251-b08, mixed mode)



  [1]: https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html
