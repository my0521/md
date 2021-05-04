---
title: SpringBoot-JDBC
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - Java
 - SpringBoot
---

记录SpringBoot环境下JDBC的使用流程

<!-- more -->

## 导入依赖
``` vbscript-html
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.18</version>
</dependency>		
<!--lombok -->
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
</dependency>
```

## 数据库建表
``` sql
SET FOREIGN_KEY_CHECKS=0;
-- ----------------------------
-- Table structure for User
-- ----------------------------
DROP TABLE IF EXISTS `User`;
CREATE TABLE `User` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(20) DEFAULT NULL,
  `age` int(11) DEFAULT '0',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;
-- ----------------------------
-- Records of User
-- ----------------------------
INSERT INTO `User` VALUES ('1', 'zhangsan', '23');
INSERT INTO `User` VALUES ('2', 'lisi', '20');
SET FOREIGN_KEY_CHECKS=1;
```

## 添加数据源配置
``` less
spring:  
  datasource:
    url: jdbc:mysql://192.168.1.100:3306/shiro?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
    username: root
    password: Wrhkw555
    driver-class-name: com.mysql.jdbc.Driver
```

## 定义实体类
``` java
@Setter
@Getter
@Data
public class User {
	private int id;
	private String name;
	private int age;
}
```

##  数据访问
``` java
@Repository
public class UserDao {
	@Resource
	JdbcTemplate jdbcTemplate;	
	public int addUser(User user) {		
		 String sql = "INSERT INTO `User` (name,age) VALUES (?, ?)";
	     return jdbcTemplate.update(sql, new Object[] { user.getName(), user.getAge() });		
	}	
	public List<User> list() {
		String sql = "SELECT * FROM `User`";
		return jdbcTemplate.query(sql, new  RowMapper<User>() {
			@Override
			public User mapRow(ResultSet rs, int rowNum) throws SQLException {
				User user = new User();
				user.setId(rs.getInt("id"));
				user.setName(rs.getString("name"));
				user.setAge(rs.getInt("age"));
				return user;
			}});
	}	
}
```

## 业务逻辑层
``` java
@Service
public class UserService {
	@Resource
	UserDao userDao;	
	public int addUser(User user) {
		return userDao.addUser(user);
	}	
	public List<User> list() {
		return userDao.list();
	}	
}
```

## Controller
``` aspectj
@RestController
public class UserController {
	@Resource
	UserService userService;
	@ResponseBody
	@PostMapping(value = "/addUser")
	public String addUser(@RequestParam("name") String name,@RequestParam("age") int age) {		
		User usr  = new User();
		usr.setName(name);
		usr.setAge(age);
		int count = userService.addUser(usr);
		return usr.toString();
	}	
	@ResponseBody
	@GetMapping(value = "/list")
	public String list() {		
		List<User> list = userService.list();
		return list.toString();
	}

}
```

## 连接池
1. 添加POM依赖
``` vbscript-html
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```
2. 配置数据源
``` bash
spring:
  datasource: 
    url: jdbc:mysql://192.168.1.100:3306/shiro?useUnicode=true&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
    username: root
    password: Wrhkw555
    driver-class-name: com.mysql.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    # 初始化时建立物理连接连接的个数
    initialSize: 5
    # 最小连接池数量
    minIdle: 5
    # 最大连接池数量
    maxActive: 20
    # 获取连接时最大等待时间(ms)，即60s
    maxWait: 60000
    # 1.Destroy线程会检测连接的间隔时间；2.testWhileIdle的判断依据
    timeBetweenEvictionRunsMillis: 60000
    # 最小生存时间ms
    minEvictableIdleTimeMillis: 300000
    # 用来检测连接是否有效的sql
    validationQuery: SELECT 1 FROM DUAL
    # 申请连接时执行validationQuery检测连接是否有效，启用会降低性能
    testOnBorrow: false
    # 归还连接时执行validationQuery检测连接是否有效，启用会降低性能
    testOnReturn: false
    # 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，
    # 执行validationQuery检测连接是否有效，不会降低性能
    testWhileIdle: true
    # 是否缓存preparedStatement，mysql建议关闭
    poolPreparedStatements: false
    # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
    filters: stat,wall,log4j
```

3. 连接池Bean配置
``` java
@Configuration
public class DruidDBConfig {
	@Bean(destroyMethod = "close", initMethod = "init")
	@ConfigurationProperties(prefix = "spring.datasource")
	public DataSource druid() {
		DruidDataSource dataSource = new DruidDataSource();
		return dataSource;
	}
	/**
	 * @Description: 配置Druid的监控
	 **/
	@Bean
	public ServletRegistrationBean statViewServlet() {
		ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
		Map<String, String> initParams = new HashMap<>();
		// druid后台管理员用户
		initParams.put("loginUsername", "admin");
		initParams.put("loginPassword", "123456");
		// 是否能够重置数据
		initParams.put("resetEnable", "false");

		bean.setInitParameters(initParams);
		return bean;
	}
	/**
	 * @Description: 配置web监控的过滤器
	 **/
	@Bean
	public FilterRegistrationBean webStatFilter() {
		FilterRegistrationBean bean = new FilterRegistrationBean(new WebStatFilter());
		// 添加过滤规则
		bean.addUrlPatterns("/*");
		Map<String, String> initParams = new HashMap<>();
		// 忽略过滤格式
		initParams.put("exclusions", "*.js, *.css, *.icon, *.png, *.jpg, /druid/*");
		bean.setInitParameters(initParams);
		return bean;
	}
}
```
4. 登陆后台监控，必须配置`ServletRegistrationBean` 
[http://localhost:8080/druid/][1]
登陆账户是在ServletRegistrationBean 中设置的`admin` `123456`

## 报错处理
1. 引入连接池后，启动报错
- 报错内容
``` bash
log4j:WARN No appenders could be found for logger (druid.sql.Connection).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info
```
- 原因
未配置log4j的配置文件引起的
- 解决：新建/src/main/resources/log4j.properties文件，添加如下内容
``` mel
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```
日志存于文件配置
``` stata
log4j.appender.FILE = org.apache.log4j.FileAppender 
log4j.appender.FILE.File = file.log 
log4j.appender.FILE.Append = false 
log4j.appender.FILE.layout = org.apache.log4j.PatternLayout 
log4j.appender.FILE.layout.ConversionPattern = [framework] % d - % c -%- 4r [ % t] %- 5p % c % x - % m % n 
```

  [1]: http://localhost:8080/druid/
