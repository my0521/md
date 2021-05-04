---
title: SpringBoot-devtools
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - Java
 - SpringBoot
---

SpringBoot集成devtools实现热部署
<!-- more -->

Spring为开发者提供了一个名为Spring-boot-devtools的模块来使Spring Boot应用支持热部署，提高开发者的开发效率，无需手动重启Spring Boot应用
## devtools的原理
深层原理是使用了两个ClassLoader，一个Classloader加载那些不会改变的类（第三方Jar包），另一个ClassLoader加载会更改的类，称为restart ClassLoader,这样在有代码更改的时候，原来的restart ClassLoader 被丢弃，重新创建一个restart ClassLoader，由于需要加载的类相比较少，所以实现了较快的重启时间
## 添加依赖
在pom.xml中添加如下依赖
``` xml
	 <dependency>
	        <groupId>org.springframework.boot</groupId>
	        <artifactId>spring-boot-devtools</artifactId>
	        <optional>true</optional>
	 </dependency>
```
## 说明
1. devtools可以实现页面热部署
即页面修改后会立即生效，这个可以直接在application.properties文件中配置spring.thymeleaf.cache=false来实现
2. 实现类文件热部署
修改java文件之后保存，等待项目重启，重启会清空session中的值，如果有用户登录，重启后需要重新输入账户密码
默认情况下，/META-INF/maven，/META-INF/resources，/resources，/static，/templates，/public这些文件夹下的文件修改不会使应用重启，但是会重新加载（devtools内嵌了一个LiveReload server，当资源发生改变时，浏览器刷新）
3. 实现对属性文件的热部署

## devtools的配置
- 在application.properties中配置spring.devtools.restart.enabled=false，此时restart类加载器还会初始化，但不会监视文件更新。
- 在SprintApplication.run之前调用System.setProperty(“spring.devtools.restart.enabled”, “false”);可以完全关闭重启支持
``` yml
spring:
  devtools:
    restart:
      enabled: true #热部署生效
      #classpath目录下的WEB-INF文件夹内容修改不重启
      exclude: WEB-INF/** 
      additional-paths: src/main/java #设置重启的目录
```

### IDEA自动编译配置
当我们修改了Java类后，IDEA默认是不自动编译的，而spring-boot-devtools又是监测classpath下的文件发生变化才会重启应用，所以需要设置IDEA的自动编译：
- File-Settings-Compiler-Build Project automatically
- ctrl + shift + alt + /,选择Registry,勾上 Compiler autoMake allow when app running

### 测试步骤
- 修改类–>保存：应用会重启
- 修改配置文件–>保存：应用会重启
- 修改页面–>保存：应用不会重启，但会重新加载，页面会刷新（原理是将spring.thymeleaf.cache设为false，参考:Spring Boot配置模板引擎）


