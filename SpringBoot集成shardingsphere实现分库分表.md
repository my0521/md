---
title: SpringBoot集成shardingsphere实现分库分表
date: 2020-07-01 20:00:48
categories: 
- SpringBoot
tags:
- SpringBoot
- 数据库
- MyBatis-plus
---

SpringBoot、shardingsphere、mybatis-plus实现分库分表
<!-- more -->

## POM依赖
``` stylus
		<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
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

		<dependency>
			<groupId>io.shardingsphere</groupId>
			<artifactId>sharding-jdbc-spring-boot-starter</artifactId>
			<version>3.1.0</version>
		</dependency>

		<dependency>
			<groupId>io.shardingsphere</groupId>
			<artifactId>sharding-jdbc-spring-namespace</artifactId>
			<version>3.1.0</version>
		</dependency>

		<!-- lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
```

## properties配置
``` stylus
spring.datasource.hikari.minimum-idle=3
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.max-lifetime =30000 // 不能小于30秒，否则默认回到1800秒
spring.datasource.hikari.connection-test-query=SELECT 1

# 数据源 ds0,ds1
sharding.jdbc.datasource.names=ds0,ds1

# 第一个数据库
sharding.jdbc.datasource.ds0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.ds0.driver-class-name=com.mysql.cj.jdbc.Driver
sharding.jdbc.datasource.ds0.jdbc-url=jdbc:mysql://121.36.64.197:3306/ds0?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
sharding.jdbc.datasource.ds0.username=root
sharding.jdbc.datasource.ds0.password=Wrhkw555

# 第二个数据库
sharding.jdbc.datasource.ds1.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.ds1.driver-class-name=com.mysql.cj.jdbc.Driver
sharding.jdbc.datasource.ds1.jdbc-url=jdbc:mysql://121.36.64.197:3306/ds1?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
sharding.jdbc.datasource.ds1.username=root
sharding.jdbc.datasource.ds1.password=Wrhkw555

# 水平拆分的数据库（表） 配置分库 + 分表策略 行表达式分片策略

# 分库策略
sharding.jdbc.config.sharding.default-database-strategy.inline.sharding-column=id
sharding.jdbc.config.sharding.default-database-strategy.inline.algorithm-expression=ds$->{id % 2}

# 分表策略 其中user为逻辑表 分表主要取决于age行
sharding.jdbc.config.sharding.tables.user.actual-data-nodes=ds$->{0..1}.user_$->{0..1}
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.sharding-column=age
# 分片算法表达式
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.algorithm-expression=user_$->{age % 2}

# 主键 UUID 18位数 如果是分布式还要进行一个设置 防止主键重复
#sharding.jdbc.config.sharding.tables.user.key-generator-column-name=id

# 打印执行的数据库以及语句
sharding.jdbc.config.props..sql.show=true
spring.main.allow-bean-definition-overriding=true
```
- 逻辑表 user
``` puppet
水平拆分的数据库（表）的相同逻辑和数据结构表的总称。例：用户数据根据主键尾数拆分为2张表，分别是user0到user1，他们的逻辑表名为user。
```
- 真实表
``` puppet
在分片的数据库中真实存在的物理表。即上个示例中的user0到user1
```
- Hint分片算法
``` undefined
对应HintShardingAlgorithm，用于处理使用Hint行分片的场景。需要配合HintShardingStrategy使用
```
- 分片策略
``` puppet
对应InlineShardingStrategy。使用Groovy的表达式，提供对SQL语句中的=和IN的分片操作支持，只支持单分片键。对于简单的分片算法，可以通过简单的配置使用，从而避免繁琐的Java代码开发，如: user$->{id % 2} 表示user表根据id模2，而分成2张表，表名称为user0到user_1
```
- 自增主键生成策略
``` stylus
通过在客户端生成自增主键替换以数据库原生自增主键的方式，做到分布式主键无重复。
采用UUID.randomUUID()的方式产生分布式主键。或者 SNOWFLAKE
```

## 项目代码实现
1. 实体类
``` scala
@Data
@EqualsAndHashCode(callSuper = true)
@Accessors(chain = true)
@TableName("user")
public class User extends Model<User> {
	private	int	 id;
	private	String	 name;
	private	int	 age;
}
```
2. dao接口
``` actionscript
public interface UserMapper extends BaseMapper {

}
```
3. 在SpringBoot主类上添加MapperScan
``` java
@MapperScan("com.my.dao")
@SpringBootApplication
public class SpringBootApp {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootApp.class, args);
	}
}
```
4. service接口
``` java
public interface IUserService extends IService {

	@Override
	boolean save(User entity);

	List getUserList();
}
```
5. service实现类
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
6. controller
``` stylus
@RestController
public class UserController {

	@Autowired
	private IUserService userService;
	
	@GetMapping("/select")
	public List<User> select() {		
		return userService.getUserList();
	}
	
	@GetMapping("/insert")
	public boolean insert(User user){		
		return userService.save(user);
	}	
}
```

## 数据库SQL
``` stylus
CREATE DATABASE IF NOT EXISTS `ds0`;
USE `ds0`;
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for user_0
-- ----------------------------
DROP TABLE IF EXISTS `user_0`;
CREATE TABLE `user_0` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- ----------------------------
-- Table structure for user_1
-- ----------------------------
DROP TABLE IF EXISTS `user_1`;
CREATE TABLE `user_1` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
SET FOREIGN_KEY_CHECKS=1;
```
## 测试
1. 插入数据
``` nix
http://localhost:8080/insert?id=1&name=lhd&age=12	
http://localhost:8080/insert?id=2&name=lhd&age=13	
http://localhost:8080/insert?id=3&name=lhd&age=14	
http://localhost:8080/insert?id=4&name=lhd&age=15
```
2. 查询
http://localhost:8080/select















