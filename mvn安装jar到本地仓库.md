---
title: mvn安装jar到本地仓库
date: 2019-05-06 20:00:48
categories: 
 - Maven
tags:
 - Maven
---

安装第三方jar包到本地mvn仓库

<!-- more -->

## 安装命令
``` nix
mvn install:install-file -Dfile=E:\alipay-trade-sdk-3.3.0.jar -DgroupId=vip.52itstyle -DartifactId=alipay-trade-sdk -Dversion=1.0.0 -Dpackaging=jar
```

## 安装支付宝SDK到mvn仓库
1. SDK下载地址 
[https://pan.baidu.com/s/1B2_uyrz2uKN1Z_Ivbv7lgw][1]

2. 项目导入依赖
``` vbscript-html
<dependency>
	<groupId>vip.52itstyle</groupId>
	<artifactId>alipay-trade-sdk</artifactId>
	<version>1.0.0</version>
</dependency>
```

  [1]: https://pan.baidu.com/s/1B2_uyrz2uKN1Z_Ivbv7lgw
