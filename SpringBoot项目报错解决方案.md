---
title: SpringBoot项目报错解决方案
date: 2018-01-01 20:00:48
categories: 
- SpringBoot
tags:
- SpringBoot
---

SpringBoot 项目报错处理及解决方案

<!-- more -->


## POM文件第一行UnKnown错误

- 原因 
SpringBoot插件与maven冲突
- 解决方案
1. 在POM的properties添加
``` stylus
	<properties>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
	</properties>
```
2. 将SpringBoot项目的版本修改为`2.1.5.RELEASE`以下

## hikari 关于 Failed to validate connection com.mysql.cj.jdbc.ConnectionImpl处理
- 原因
SpringBoot2开始配置文件有所变化
- 解决方案
在properties中添加如下配置
``` stylus
spring.datasource.hikari.minimum-idle=3
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.max-lifetime =30000 // 不能小于30秒，否则默认回到1800秒
spring.datasource.hikari.connection-test-query=SELECT 1
```


