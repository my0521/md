---
title: SpringBoot集成MyBatis-Plus实现数据库访问
date: 2020-07-01 20:00:48
categories: 
- SpringBoot
tags:
- SpringBoot
- 数据库
- MyBatis
---

在学习sharding分库分表的过程中，在一个项目中看到使用了MyBatis-Plus，与MyBatis的使用有些差别但更显简单，此处略作记录
<!-- more -->

## 导入POM依赖
``` vbscript-html
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-freemarker -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-freemarker</artifactId>
		</dependency>
		<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

		<!-- https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.3.2</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.1.10</version>
		</dependency>

		<!-- lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
```

## 配置

``` stylus
# db
spring.datasource.url=jdbc:mysql://121.36.64.197:3306/ds0?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=Wrhkw555
# spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

```

## 数据库脚本
``` stylus
SET FOREIGN_KEY_CHECKS=0;
-- ----------------------------
-- Table structure for user_0
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## Mapper接口
1. 在启动类上添加Mapper接口的扫描或者在Mapper接口上添加@mapper注解
``` java
@MapperScan("com.my.dao")
@SpringBootApplication
public class SpringBootApp {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootApp.class, args);
	}
}
```
2. service接口
``` java
public interface IUserService extends IService {

	@Override
	boolean save(User entity);

	List getUserList();
}
```


3. service实现
``` scala
@Service
public class UserService extends ServiceImpl implements IUserService {

	@Override
	public boolean save(User entity) {		
		return super.save(entity);
	}

	@Override
	public List getUserList() {		
		return baseMapper.selectList(Wrappers.lambdaQuery());
	}

}
```
4. controller
``` aspectj
@RequestMapping("user")
@RestController
public class UserController {

	@Autowired
	private IUserService userService;
	
	@GetMapping("/select")
	public List select() {		
		return userService.getUserList();
	}	
	@GetMapping("/insert")
	public boolean insert(User user){
		
		return userService.save(user);
	}	
}
```

## 测试
1. 插入数据
``` nix
http://localhost:8080/user/insert?id=1&name=lhd&age=12	
http://localhost:8080/goods/insert?id=2&name=lhd&age=13	
http://localhost:8080/user/insert?id=3&name=lhd&age=14	
http://localhost:8080/goods/insert?id=4&name=lhd&age=15
```
2. 查询
http://localhost:8080/goods/select
http://localhost:8080/user/select




