---
title: SpringBoot-Redis
date: 2020-05-04 18:49:48
categories:
- SpringBoot
tags:
- Redis
- SpringBoot
- Java
---

Redis与SpringBoot集成
<!-- more -->


## 添加POM依赖

``` xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-pool2</artifactId>
</dependency>
```
## 添加redis相关配置

``` stylus
spring.redis.database=0
spring.redis.host=192.168.1.100
spring.redis.port=6379
#默认为空
spring.redis.password=123456

#pool 最大连接数
spring.redis.lettuce.pool.max-active= 8
#pool最大阻塞等待时间(负值没有限制)
#spring.redis.lettuce.pool.max-wait= -1 
#pool最大空闲链接 默认8
spring.redis.lettuce.pool.max-idle= 8 
#pool最小空闲链接 默认0
spring.redis.lettuce.pool.min-idle= 0 
```
## 添加Redis的Bean

``` java
package com.my.config;

import java.io.Serializable;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

	@Bean
	public RedisTemplate<String, Serializable> redisTemplate(LettuceConnectionFactory connectionFactory){
		RedisTemplate<String, Serializable> redisTemplate = new RedisTemplate<>();
		redisTemplate.setKeySerializer(new StringRedisSerializer());
		redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer() );
		redisTemplate.setConnectionFactory(connectionFactory);
		return redisTemplate;
	}	
}
```

## 添加一个实体类User

``` java
package com.my.bean;

import java.io.Serializable;

public class User implements Serializable{
	
	private Integer id;
	private String name;
	private String sex;
	
	public User() {}

	public User(Integer id, String name, String sex) {
		super();
		this.id = id;
		this.name = name;
		this.sex = sex;
	}

	//Getter and Setter
	// toString()
}
```

## 编写单元测试
在方法上右键>Run as> JUnit test
``` java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringBootDemoRedisApplicationTests {

	@Autowired
	private RedisTemplate redisTemplate; 
	
	@Test
	public void testRedis() {
		User user = new User(1,"楠楠","female");
		ValueOperations<String, User> operations = redisTemplate.opsForValue();
		operations.set("nan", user);
		boolean exists = redisTemplate.hasKey("nan") ;
		System.out.println("Redis是否存在key-{'nan'}" + exists);
		User getUser = (User)redisTemplate.opsForValue().get("nan");
		System.out.println("User from redis:"+ getUser.toString());		
	}
}
```










