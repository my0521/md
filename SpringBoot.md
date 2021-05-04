---
title: SpringBoot
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - Java
 - SpringBoot
---
SpringBoot框架环境搭建
<!-- more -->
## SpringBoot
　简化Spring应用开发的一个框架；整个Spring技术栈的一个大整合；J2EE开发的一站式解决方案；
### 环境准备
- jdk1.8：
- maven3.x：
- IntelliJIDEA、STS
- MySql：数据库开发需要

### 解决
- Spring Boot：J2EE一站式解决方案
- Spring Cloud ：分布式整体解决问题

### 优点
- 快速创建独立运行的Spring项目以及与主流框架集成　
- 嵌入的Tomcat，无需打包成WAR包
- starters自动依赖与版本控制
- 大量自动配置，简化开发，也可修改默认值
- 无需配置xml，无代码生成，开箱即用
- 准生产环境的运行时应用监控
- 与云计算天然集成

## Maven 构建项目
### IDE需要安装maven插件
 - 访问 [https://start.spring.io/][1]
 - 填写项目信息，可以导入自己需要的包
 - 使用EXPLORE预览项目结构及配置信息
 - 点击GENERATE 生成SpringBoot项目，并下载
 - 在自己的IDE中导入刚才创建的项目
 - 在导入项目后，IDE会自动下载依赖的jar包

### STS报Maven Configuration Problem
- 原因：是因为SpringBoot版本与pom中的插件`spring-boot-maven-plugin`版本冲突导致
- 解决方案：将`spring-boot-starter-parent`的版本改为2.1.5以下即可解决，这里使用2.1.4

### 添加自定义controller
1. 在启动类所在的包下新增`controller`包
2. 在controller包中新增类`HelloController`
3. 在HelloController类上添加`@RestController`注解
4. 在类中添加一个方法，方法名自定义，返回String
5. 在方法上添加注解`@RequestMapping("/hello")`
6. 运行启动类
7. 访问[http://localhost:8080/hello][2]
``` java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class HelloController {
	@RequestMapping("/hello")
    public String hello(){
        return "Hello SpringBoot!";
    }
}
```


  [1]: https://start.spring.io/
  [2]: http://localhost:8080/hello
