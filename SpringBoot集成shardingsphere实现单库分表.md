---
title: SpringBoot集成shardingsphere实现单库分表
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

在项目`SpringBoot集成shardingsphere实现分库分表`的基础上稍作修改

## 新增数据库
``` stylus
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for goods
-- ----------------------------
DROP TABLE IF EXISTS `goods`;
CREATE TABLE `goods` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

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

## properties配置
``` stylus
spring.datasource.hikari.minimum-idle=3
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.max-lifetime =50000 // 不能小于30秒，否则默认回到1800秒
spring.datasource.hikari.connection-test-query=SELECT 1

# 数据源 ds0,ds1
sharding.jdbc.datasource.names=ds0

# 第一个数据库
sharding.jdbc.datasource.ds0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.ds0.driver-class-name=com.mysql.cj.jdbc.Driver
sharding.jdbc.datasource.ds0.jdbc-url=jdbc:mysql://121.36.64.197:3306/ds0?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
sharding.jdbc.datasource.ds0.username=root
sharding.jdbc.datasource.ds0.password=root

# 分表策略 其中user为逻辑表 分表主要取决于age行
sharding.jdbc.config.sharding.tables.user.actual-data-nodes=ds0.user_$->{0..1}
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.sharding-column=id

# 分片算法表达式
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.algorithm-expression=user_$->{id % 2}

# 主键 UUID 18位数 如果是分布式还要进行一个设置 防止主键重复
#sharding.jdbc.config.sharding.tables.user.key-generator-column-name=id

# 打印执行的数据库以及语句
sharding.jdbc.config.props..sql.show=true
spring.main.allow-bean-definition-overriding=true
```

## 实现goods表的controller，dao，service
参考分库分表项目的user实现
1. 请求路由
``` java
@RequestMapping("goods")
@RestController
public class GoodsController {
	@Autowired
	private IGoodsService goodsService;
	
	@GetMapping("/select")
	public List<Goods> select() {
		
		return goodsService.getUserList();
	}
	
	@GetMapping("/insert")
	public boolean insert(Goods goods){
		
		return goodsService.save(goods);
	}	
}
```

## 测试
1. 插入数据
``` nix
http://localhost:8080/goods/insert?id=1&name=lhd&age=12	
http://localhost:8080/goods/insert?id=2&name=lhd&age=13	
http://localhost:8080/goods/insert?id=3&name=lhd&age=14	
http://localhost:8080/goods/insert?id=4&name=lhd&age=15
```
2. 查询
http://localhost:8080/goods/select
http://localhost:8080/user/select




