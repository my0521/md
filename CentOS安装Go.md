---
title: CentOS安装Go
date: 2020-07-01 20:00:48
categories: 
- Go
tags:
- Go
---

CentOS安装Go以及源码编译安装

<!-- more -->


Go语言中文网[https://studygolang.com/dl][1]
Go官网 [https://golang.google.cn][2]


## 下载安装
1. 下载安装包
``` vim
wget https://studygolang.com/dl/golang/go1.14.6.linux-amd64.tar.gz
```
2. 解压到安装目录/usr/local/
``` stylus
tar -zxvf go1.14.6.linux-amd64.tar.gz -C /usr/local/
```
3. 配置go环境变量
- `vim /etc/profile`
``` bash
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```
- `source /etc/profile`
4. 验证
``` vim
go version
```
go version go1.14.6 linux/amd64
5. 查看Go的环境变量信息
``` mel
go env
```



## helloworld
1. vim hello.go
``` swift
package main

import "fmt"

func main(){
        fmt.Printf("hello go\n")
}
```
2. 运行hello.go
``` go
go run hello.go 
```

## 源码编译安装
1. 下载源码
``` vim
git clone https://go.googlesource.com/go goroot
wget https://studygolang.com/dl/golang/go1.14.6.src.tar.gz 
```
2. 解压到安装目录/usr/local/src
``` stylus
tar -zxvf go1.14.6.src.tar.gz  -C /usr/local
```
3. 进入源码目录
``` gradle
cd /usr/local/go/src/
```
4. 编译
``` go
./make.bash
```
5. 配置go环境变量
- `vim /etc/profile`
``` bash
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```
- `source /etc/profile`
6. 验证
``` vim
go version
```



  [1]: https://studygolang.com/dl
  [2]: https://golang.google.cn
