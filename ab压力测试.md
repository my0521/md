---
title: ab压力测试 
date: 2018-01-01 20:00:48
categories: 
- 测试
tags:
- ab
- 测试
---

ab测试工具安装、参数的介绍以及使用方法

<!-- more -->

Apache Benchmark(简称ab) 是Apache安装包中自带的压力测试工具 ，简单易用

## 安装
``` sql
yum -y install httpd-tools
```
- 查看安装路径 `which ab`

- 查看版本 `ab -V`

## 参数说明
``` haml
-n	即requests，用于指定压力测试总共的执行次数。
-c	即concurrency，用于指定的并发数。
-t	即timelimit，等待响应的最大时间(单位：秒)。
-b	即windowsize，TCP发送/接收的缓冲大小(单位：字节)。
-p	即postfile，发送POST请求时需要上传的文件，此外还必须设置-T参数。
-u	即putfile，发送PUT请求时需要上传的文件，此外还必须设置-T参数。
-T	即content-type，用于设置Content-Type请求头信息，例如：application/x-www-form-urlencoded，默认值为text/plain。
-v	即verbosity，指定打印帮助信息的冗余级别。
-w	以HTML表格形式打印结果。
-i	使用HEAD请求代替GET请求。
-x	插入字符串作为table标签的属性。
-y	插入字符串作为tr标签的属性。
-z	插入字符串作为td标签的属性。
-C	添加cookie信息，例如："Apache=1234"(可以重复该参数选项以添加多个)。
-H	添加任意的请求头，例如："Accept-Encoding: gzip"，请求头将会添加在现有的多个请求头之后(可以重复该参数选项以添加多个)。
-A	添加一个基本的网络认证信息，用户名和密码之间用英文冒号隔开。
-P	添加一个基本的代理认证信息，用户名和密码之间用英文冒号隔开。
-X	指定使用的和端口号，例如:"121.36.64.197:88"。
-V	打印版本号并退出。
-k	使用HTTP的KeepAlive特性。
-d	不显示百分比。
-S	不显示预估和警告信息。
-g	输出结果信息到gnuplot格式的文件中。
-e	输出结果信息到CSV格式的文件中。
-r	指定接收到错误信息时不退出程序。
-h	显示用法信息，其实就是ab -help。
```
## 使用
1. 模拟100个用户并发请求，总共请求10000次
2. 命令模板：
`ab -c 100 -n 10000 http://121.36.64.197/`
3.  测试结果

``` groovy
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 121.36.64.197 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        nginx/1.18.0
Server Hostname:        121.36.64.197
Server Port:            80

Document Path:          /
Document Length:        125985 bytes

Concurrency Level:      100
Time taken for tests:   166.068 seconds
Complete requests:      10000
Failed requests:        2 (失败的请求数)
   (Connect: 0, Receive: 0, Length: 2, Exceptions: 0)
Write errors:           0
Total transferred:      1262173510 bytes
HTML transferred:       1259793510 bytes
Requests per second:    60.22 [#/sec] (mean)[吞吐率]
Time per request:       1660.681 [ms] (mean)
Time per request:       16.607 [ms] (mean, across all concurrent requests)
Transfer rate:          7422.21 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0  494 1206.2      2   31076
Processing:    11  568 3350.6    223  149818
Waiting:        2   54 457.2      2   30089
Total:         13 1063 3579.6    287  149821

Percentage of the requests served within a certain time (ms)
  50%    287
  66%   1026
  75%   1229
  80%   1258
  90%   2096
  95%   3241
  98%   6027
  99%   8724
 100%  149821 (longest request)
```


