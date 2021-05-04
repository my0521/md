---
title: docker部署SpringBoot项目
date: 2019-05-06 20:00:48
categories: 
 - docker
tags:
 - SpringBoot
 - docker
---
 
 使用docker部署springboot项目

<!-- more -->


## 创建 SpringBoot项目
``` java
@RestController
public class HelloController {
	    @RequestMapping("/")
	    @ResponseBody
	    public String hello() {
	        return "Hello, SpringBoot With Docker";
	    }
}
```

## 打包

### 命令行打包
1. 进入项目根目录
2. 清理项目 `mvn clean` 
3. 打包，忽略test测试 `mvn package -Dmaven.test.skip=true`

## docker 部署
1. 编写Dockerfile文件
``` dockerfile
# Docker image for springboot file run
# VERSION 0.0.1
# Author: eangulee
# 基础镜像使用java
FROM java:8
# 作者
MAINTAINER eangulee <yeal136@163.com>
# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp
VOLUME /tmp 
# 将jar包添加到容器中并更名为app.jar
ADD Docker-SpringBoot-0.0.1.jar app.jar 
# 运行jar包
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```
2. 在服务器上新建目录docker，将Docker-SpringBoot-0.0.1.jar与Dockerfile上传至目录
``` mel
[root@hecs-x-medium-2-linux-20200606090800 docker]# ls
Dockerfile  Docker-SpringBoot-0.0.1.jar
```
3. 制作镜像[时间较长] 
``` erlang
docker build -t springbootdocker .
```
-t 参数是指定此镜像的tag名
``` haml
Sending build context to Docker daemon  16.73MB
Step 1/6 : FROM java:8
8: Pulling from library/java
5040bd298390: Pull complete 
fce5728aad85: Pull complete 
76610ec20bf5: Pull complete 
60170fec2151: Pull complete 
e98f73de8f0d: Pull complete 
11f7af24ed9c: Pull complete 
49e2d6393f32: Pull complete 
bb9cdec9c7f3: Pull complete 
Digest: sha256:c1ff613e8ba25833d2e1940da0940c3824f03f802c449f3d1815a66b7f8c0e9d
Status: Downloaded newer image for java:8
 ---> d23bdf5b1b1b
Step 2/6 : MAINTAINER eangulee 
 ---> Running in 9c5dfb7761a9
Removing intermediate container 9c5dfb7761a9
 ---> 2205d34a65f8
Step 3/6 : VOLUME /tmp
 ---> Running in 7d52a9934cec
Removing intermediate container 7d52a9934cec
 ---> 5b9642873e35
Step 4/6 : ADD Docker-SpringBoot-0.0.1.jar app.jar
 ---> 104f04273ec0
Step 5/6 : RUN bash -c 'touch /app.jar'
 ---> Running in fdb176364c1e
Removing intermediate container fdb176364c1e
 ---> cb96d2714a3c
Step 6/6 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
 ---> Running in 957d72bc9930
Removing intermediate container 957d72bc9930
 ---> f21da3326bc2
Successfully built f21da3326bc2
Successfully tagged springbootdocker:latest
```


4. 查看镜像
``` nginx
docker images
```
5. 启动容器
``` stata
docker run -d -p 8080:8085 springbootdocker 
```
-d 参数是让容器后台运行 
-p 是做端口映射，此时将服务器中的8080端口映射到容器中的8085(项目中端口配置的是8085)端口
6. 访问
http://121.36.64.197:8080/


