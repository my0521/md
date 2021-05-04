
---
title: ffmpeg安装
date: 2019-05-06 20:00:48
categories: 
 - ffmpeg
tags:
 - ffmpeg
 - CentOS
---

CentOS下编译安装ffmpeg
<!-- more -->
ffmpeg下载 [https://johnvansickle.com/ffmpeg/release-source/][1]



## ffmpeg安装
1. 下载
``` vim
wget https://johnvansickle.com/ffmpeg/release-source/ffmpeg-4.1.tar.xz
```
2. 解压
``` stylus
tar -xvJf ffmpeg-4.1.tar.xz  -C /usr/local/
```
3. 配置
``` brainfuck
./configure --enable-shared --prefix=/usr/local/ffmpeg
```
- `--enable-shared` 允许生成动态链接库
- `--prefix=/usr/local/ffmpeg`指定安装的目录
4. 安装
``` go
make 
make install
```
5. 修改文件`/etc/ld.so.conf`
- vim /etc/ld.so.conf
- 输入以下内容
``` stata
include ld.so.conf.d/*.conf
/usr/local/ffmpeg/lib/
```
- ldconfig
6. 查看版本
``` gradle
/usr/local/ffmpeg/bin/ffmpeg -version
```
7. 配置环境变量
- vim /etc/profile
``` stata
PATH=$PATH:/usr/local/ffmpeg/bin
```
- source /etc/profile
8. ffmpeg -version

## 安装yasm
yasm下载 [http://www.tortall.net/projects/yasm/releases/][2]
1. 下载
``` elixir
wget http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
```
2. 解压
``` stylus
tar zxvf yasm-1.3.0.tar.gz
```
3. 安装
``` vim
cd yasm-1.3.0
./configure
make && make install
```

## 报错
1. `nasm/yasm not found or too old. Use --disable-x86asm for a crippled build.`
解决方案： `安装yasm`


  [1]: https://johnvansickle.com/ffmpeg/release-source/
  [2]: http://www.tortall.net/projects/yasm/releases/
