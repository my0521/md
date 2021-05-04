---
title: SpringBoot项目集成dataway
date: 2018-01-01 20:00:48
categories: 
- SpringBoot
tags:
- SpringBoot
---

Dataway 是 Hasor 生态中的一员，因此在  Spring 中使用 Dataway 首先要做的就是打通两个生态,将 Hasor 和 Spring Boot 整合起来

<!-- more -->

## 简介
- Dataway 是基于 DataQL 服务聚合能力，为应用提供的一个接口配置工具。使得使用者无需开发任何代码就配置一个满足需求的接口。整个接口配置、测试、冒烟、发布。一站式都通过 Dataway 提供的 UI 界面完成。UI 会以 Jar 包方式提供并集成到应用中并和应用共享同一个 http 端口，应用无需单独为 Dataway 开辟新的管理端口。    
- 这种内嵌集成方式模式的优点是，可以使得大部分老项目都可以在无侵入的情况下直接应用 Dataway。进而改进老项目的迭代效率，大大减少企业项目研发成本。    
- Dataway 工具化的提供 DataQL 配置能力。这种研发模式的变革使得，相当多的需求开发场景只需要配置即可完成交付。从而避免了从数据存取到前端接口之间的一系列开发任务，例如：Mapper、BO、VO、DO、DAO、Service、Controller 统统不在需要

## 导入POM依赖
``` vbscript-html
	<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--hasor-spring 负责 Spring 和 Hasor 框架之间的整合。-->
        <dependency>
            <groupId>net.hasor</groupId>
            <artifactId>hasor-spring</artifactId>
            <version>4.1.3</version>
        </dependency>
        <!--hasor-dataway 是工作在 Hasor 之上，利用 hasor-spring 我们就可以使用 dataway-->
        <!-- 4.1.3-fix20200414 包存在UI资源缺失问题 -->
        <dependency>
            <groupId>net.hasor</groupId>
            <artifactId>hasor-dataway</artifactId>
            <version>4.1.3-fix20200414</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.21</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
```

## properties配置
``` tex
# \u662F\u5426\u542F\u7528 Dataway \u529F\u80FD\uFF08\u5FC5\u9009\uFF1A\u9ED8\u8BA4false\uFF09
HASOR_DATAQL_DATAWAY=true
# \u662F\u5426\u5F00\u542F Dataway \u540E\u53F0\u7BA1\u7406\u754C\u9762\uFF08\u5FC5\u9009\uFF1A\u9ED8\u8BA4false\uFF09
HASOR_DATAQL_DATAWAY_ADMIN=true
# dataway  API\u5DE5\u4F5C\u8DEF\u5F84\uFF08\u53EF\u9009\uFF0C\u9ED8\u8BA4\uFF1A/api/\uFF09
HASOR_DATAQL_DATAWAY_API_URL=/api/
# dataway-ui \u7684\u5DE5\u4F5C\u8DEF\u5F84\uFF08\u53EF\u9009\uFF0C\u9ED8\u8BA4\uFF1A/interface-ui/\uFF09
HASOR_DATAQL_DATAWAY_UI_URL=/interface-ui/
# SQL\u6267\u884C\u5668\u65B9\u8A00\u8BBE\u7F6E\uFF08\u53EF\u9009\uFF0C\u5EFA\u8BAE\u8BBE\u7F6E\uFF09
HASOR_DATAQL_FX_PAGE_DIALECT=mysql

# db
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123456
# spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
# druid
spring.datasource.druid.initial-size=3
spring.datasource.druid.min-idle=3
spring.datasource.druid.max-active=10
spring.datasource.druid.max-wait=60000
spring.datasource.druid.stat-view-servlet.login-username=admin
spring.datasource.druid.stat-view-servlet.login-password=admin
spring.datasource.druid.filter.stat.log-slow-sql=true
spring.datasource.druid.filter.stat.slow-sql-millis=1
```
## 在启动类上添加注解
``` java
@EnableHasor
@EnableHasorWeb
@SpringBootApplication
public class SpringbootApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }    
}
```

## 数据源设置到 Hasor 容器
``` java
import net.hasor.core.ApiBinder;
import net.hasor.core.DimModule;
import net.hasor.db.JdbcModule;
import net.hasor.db.Level;
import net.hasor.spring.SpringModule;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@DimModule
@Component
public class ExampleModule implements SpringModule {
    @Autowired
    private DataSource dataSource = null;
    
    @Override
    public void loadModule(ApiBinder apiBinder) throws Throwable {
        // .DataSource form Spring boot into Hasor
        apiBinder.installModule(new JdbcModule(Level.Full, this.dataSource));
    }
}
```

## 在数据库中新增2张表
``` sql
CREATE TABLE `interface_info` (
    `api_id`          int(11)      NOT NULL AUTO_INCREMENT   COMMENT 'ID',
    `api_method`      varchar(12)  NOT NULL                  COMMENT 'HttpMethod：GET、PUT、POST',
    `api_path`        varchar(512) NOT NULL                  COMMENT '拦截路径',
    `api_status`      int(2)       NOT NULL                  COMMENT '状态：0草稿，1发布，2有变更，3禁用',
    `api_comment`     varchar(255)     NULL                  COMMENT '注释',
    `api_type`        varchar(24)  NOT NULL                  COMMENT '脚本类型：SQL、DataQL',
    `api_script`      mediumtext   NOT NULL                  COMMENT '查询脚本：xxxxxxx',
    `api_schema`      mediumtext       NULL                  COMMENT '接口的请求/响应数据结构',
    `api_sample`      mediumtext       NULL                  COMMENT '请求/响应/请求头样本数据',
    `api_create_time` datetime     DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    `api_gmt_time`    datetime     DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
    PRIMARY KEY (`api_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COMMENT='Dataway 中的API';
 
CREATE TABLE `interface_release` (
    `pub_id`          int(11)      NOT NULL AUTO_INCREMENT   COMMENT 'Publish ID',
    `pub_api_id`      int(11)      NOT NULL                  COMMENT '所属API ID',
    `pub_method`      varchar(12)  NOT NULL                  COMMENT 'HttpMethod：GET、PUT、POST',
    `pub_path`        varchar(512) NOT NULL                  COMMENT '拦截路径',
    `pub_status`      int(2)       NOT NULL                  COMMENT '状态：0有效，1无效（可能被下线）',
    `pub_type`        varchar(24)  NOT NULL                  COMMENT '脚本类型：SQL、DataQL',
    `pub_script`      mediumtext   NOT NULL                  COMMENT '查询脚本：xxxxxxx',
    `pub_script_ori`  mediumtext   NOT NULL                  COMMENT '原始查询脚本，仅当类型为SQL时不同',
    `pub_schema`      mediumtext       NULL                  COMMENT '接口的请求/响应数据结构',
    `pub_sample`      mediumtext       NULL                  COMMENT '请求/响应/请求头样本数据',
    `pub_release_time`datetime     DEFAULT CURRENT_TIMESTAMP COMMENT '发布时间（下线不更新）',
    PRIMARY KEY (`pub_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COMMENT='Dataway API 发布历史。';
 
create index idx_interface_release on interface_release (pub_api_id);
```

## 启动
当看到日志中 “dataway api workAt /api/” 、 dataway admin workAt /interface-ui/ 信息时，就可以确定 Dataway 的配置已经生效了
1. 访问 [http://127.0.0.1:8080/interface-ui/][1] 添加接口
2. 编写接口
3. save
4. 冒烟
5. 发布

之后就可以通过`http://127.0.0.1:8080/api/xxx`访问新增的接口

  [1]: http://127.0.0.1:8080/interface-ui/
