---
title: SpringBoot配置文件
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - Java
 - SpringBoot
---
SpringBoot配置文件参数的获取及使用
<!-- more -->

## 配置参数间的引用
可以在application.properties(yml)中使用${...}引用已有变量

``` stata
test:
  greet: Hello World Springboot env!
  greet1: ${test.greet} 2
```

## 配置随机变量
``` livecodeserver
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```

## 配置文件优先级
1. application.properties(yml)文件可以放在4个位置：
- 外置，在相对于应用程序运行目录的/congfig子目录里。
- 外置，在应用程序运行的目录里
- 内置，在config包内
- 内置，在Classpath根目录

同样，这个列表按照优先级排序，也就是说，src/main/resources/config下application.properties覆盖src/main/resources下application.properties中相同的属性，此外，如果你在相同优先级位置同时有application.properties和application.yml，那么application.yml里面的属性就会覆盖application.properties里的属性，SpringBoot启动时会按照优先级顺序加载配置文件。
2. 自定义配置文件`application.properties`位置
如果有自定义的配置文件不在上述的4个路径下，可以通过spring.config.location`enter code here` 环境变量来指定启动时要加载的配置文件位置:
``` stylus
//指定加载classpath:/xxconfig/下的配置文件
spring.config.location=classpath:/xxconfig/
```
- java -jar properties-0.0.1-SNAPSHOT.jar --spring.config.location=classpath:/javaboy/

## 使用profiles实现多环境配置
在开发过程中，不同的人会用到不同的配置，比如开发人员和测试人员，有如下3个配置文件：
1. 主配置文件src/main/resources/application.yml 
2. dev环境src/main/resources/application-dev.yml
3. test环境src/main/resources/application-test.yml

``` less
spring:
  profiles:
    active: dev #dev or test，此处激活dev环境
```
 
## 获取配置文件属性
1.  在配置文件中自定义属性
``` vim
test:
  greet: Hello World Springboot 2!
```
-  通过@value注解来读取 `@Value("${test.greet}")`

	``` java
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RestController;
	import org.springframework.beans.factory.annotation.Value;
	@RestController
	public class HelloController {
		
		@Value("${test.greet}")
	    String msg;
		@RequestMapping("/hello")
	    public String hello(){
	        return this.msg;
	    }
	}
	```
-  使用Environment方式

	``` java
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RestController;
	import javax.annotation.Resource;
	import org.springframework.core.env.Environment;
	@RestController
	public class HelloController {
		
		@Resource
		Environment env;
	
		@RequestMapping("/hello")
	    public String hello(){
	        return env.getProperty("test.greet");
	    }
	}
	```
-  自定义类读取配置信息（类型安全的属性注入）
为了不破坏核心文件的原生态，但又需要有自定义的配置属性存在，可以在src/main/resources下新建一个properties文件存放自定义属性my.properties
	
	``` java
	import org.springframework.boot.context.properties.ConfigurationProperties;
	import org.springframework.context.annotation.PropertySource;
	import org.springframework.stereotype.Component;
	
	@Component
	@PropertySource("classpath:/my.properties")
	@ConfigurationProperties(prefix = "user")
	public class ConfigBean {
		
		private	String name;
		
		private	String passwd;
		
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public String getPasswd() {
			return passwd;
		}
	
		public void setPasswd(String passwd) {
			this.passwd = passwd;
		}
	
		@Override
		public String toString() {
			return "My [name=" + name + ", passwd=" + passwd + "]";
		}	
	}
	```

	- 使用
	``` aspectj
	@Resource
	ConfigBean configBean;
		
	@RequestMapping("/user")
	public String User(){
	    return configBean.toString();
	}
	```
	
这里，主要是引入 @ConfigurationProperties(prefix = “user”) 注解，并且配置了属性的前缀，此时会自动将 Spring 容器中对应的数据注入到对象对应的属性中，就不用通过 @Value 注解挨个注入了，减少工作量并且避免出错。




