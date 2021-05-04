---
title: SpringBoot-SwaggerUi
date: 2019-05-06 20:00:48
categories: 
 - SpringBoot
tags:
 - SpringBoot
 - SwaggerUi
---
SwaggerUi就是自动生成接口文档的这么一个类似于插件的工具，可以直接进行接口调试

<!-- more -->

## 导入依赖
1. 定义版本号
``` vbscript-html
<properties>
    <swagger.version>2.6.1</swagger.version>
</properties>
```
2. 添加POM依赖
``` stata
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>${swagger.version}</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>${swagger.version}</version>
</dependency>  
```

## 配置SwaggerConfig
``` actionscript
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
@Configuration  //在springboot里面专门为了加载配置文件的标签
@EnableSwagger2   //自动加载配置文件
public class SwaggerConfig {
    @Bean
    public Docket api(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .pathMapping("/")
                .select()
                .paths(PathSelectors.regex("/.*"))   //匹配那些访问的方法
                .build();
    }
    private ApiInfo apiInfo() {
        //http://localhost:8888/swagger-ui.html
        return new ApiInfoBuilder().title("我的接口文档")
                .contact(new Contact("my","","yeal136@163.com"))
                .description("这是我的swaggerui生成的接口文档")
                .version("1.0.0.0")
                .build();
    }
}
```

## 注解展示接口
1. 在接口类上添加@Api
``` less
@RestController
@Api(value = "/",description = "UserController")
public class UserController {
	//。。。
}
```
2. 在具体的接口上添加@ApiOperation
- value 描述接口的具体功能
- httpMethod 调用接口的方式
``` aspectj
@RestController
@Api(value = "/",description = "UserController")
public class UserController {
	@ResponseBody
	@PostMapping(value = "/addUser")
    	@ApiOperation(value = "添加一个User",httpMethod ="POST")
	public String addUser(@RequestParam("name") String name,@RequestParam("age") int age) {		
		User usr  = new User();
		usr.setName(name);
		usr.setAge(age);
		int count = userService.addUser(usr);
		return usr.toString();
	}
}
```


## 说明
在启动类上新增@EnableSwagger2注解，在controller中可以不用@Api与@ApiOperation(value = "添加一个User",httpMethod ="POST")

## 调试
1. 启动项目
2. 进入[http://localhost:8080/swagger-ui.html][1]





  


  [1]: http://localhost:8080/swagger-ui.html
