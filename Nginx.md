---
title: Nginx安装 
date: 2020-05-04 18:49:48
categories: 
- Nginx 
tags:
- Nginx 
- CentOS
---
记录CentOS下源码安装Nginx的流程
<!-- more -->

中文网 [http://nodejs.cn/][1]

## 安装nginx

### 更新依赖
``` sql
yum install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

### 下载nginx源码包，并下载解压到指定目录
``` stata
//创建一个文件夹
cd /tmp/
//远程下载tar包
wget http://nginx.org/download/nginx-1.18.0.tar.gz
//远程下载tar包
tar -zxvf nginx-1.18.0.tar.gz -C /usr/local/src/
```

## 下载模块nginx-rtmp-module
此模块用于使用nginx做rtmp服务器推流直播使用，在安装nginx时，可以通过参数配置
1. `cd  /usr/local/src/`
2. `git clone https://github.com/arut/nginx-rtmp-module.git` 
3. `./configure --prefix=/usr/local/nginx  --add-module=../nginx-rtmp-module  --with-http_ssl_module`   
4. rtmp服务器配置
``` nginx
events {
    worker_connections  1024;
}

rtmp {
    server{
        listen 1993;
        application my {
            live on;
            record off;
        }
    }
}

http {
}
```
5. 编译错误解决
- 错误描述
``` groovy
/usr/local/src/nginx-rtmp-module/ngx_rtmp_eval.c: 在函数‘ngx_rtmp_eval’中:
/usr/local/src/nginx-rtmp-module/ngx_rtmp_eval.c:160:17: 错误：this statement may fall through [-Werror=implicit-fallthrough=]
                 switch (c) {
                 ^~~~~~
/usr/local/src/nginx-rtmp-module/ngx_rtmp_eval.c:170:13: 附注：here
             case ESCAPE:
             ^~~~
cc1：所有的警告都被当作是错误
make[1]: *** [objs/Makefile:1368：objs/addon/nginx-rtmp-module/ngx_rtmp_eval.o] 错误 1
make[1]: 离开目录“/usr/local/src/nginx-1.18.0"
```
- 解决方法
``` groovy
打开/usr/local/src/nginx-rtmp-module/ngx_rtmp_eval.c文件
vim /usr/local/src/nginx-rtmp-module/ngx_rtmp_eval.c
在第169行（空行）插入  /* fall through */ ，保存退出
```



### 开始安装
``` stata
//进入nginx源码目录
cd /usr/local/src/nginx-1.18.0/
//执行命令,生成makefile文件 /usr/local/nginx为安装的nginx目录
./configure --prefix=/usr/local/nginx
//执行make命令 ** //执行make install命令
make && make install
```

## 配置文件
``` stata
vim  /usr/local/nginx/conf/nginx.conf
```

### 测试
``` gradle
/usr/local/nginx/sbin/nginx -V
```


### 启动
``` stata
//启动
/usr/local/nginx/sbin/nginx 

//停止
/usr/local/nginx/sbin/nginx  -s stop 

//重启，这个命令是nginx已经启动的前提下生效，一般改配置文件时，要重启
/usr/local/nginx/sbin/nginx  -s reload
```

### 查看进程
``` vim
ps -ef|grep nginx
netstat -ntlp | grep nginx
ps aux|grep nginx
```

### 以服务的方式启动
``` sql
pkill nginx
systemctl start nginx
#查看服务状态
systemctl status nginx
```

### 配置服务自动启动
``` bash
systemctl enable nginx
```


  [1]: http://nodejs.cn/
