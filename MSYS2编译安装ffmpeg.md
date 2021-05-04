---
title: MSYS2编译安装ffmpeg
date: 2019-05-06 20:00:48
categories: 
 - ffmpeg
tags:
 - ffmpeg
 - MSYS2
---

记录Windows下使用MSYS2编译ffmpeg过程
<!-- more -->

## 安装MSYS2

参考MSYS2安装

## 工具包安装
1. 启动`msys2_shell.bat`，在命令行输入
``` go
pacman -S make yasm diffutils pkg-config
```
2. 安装gcc
默认的MSYS2安装时不带mingw32和mingw64的
- `pacman -Sl | grep gcc`
``` scss
mingw32 mingw-w64-i686-gcc 10.2.0-1 [已安装]
mingw32 mingw-w64-i686-gcc-ada 10.2.0-1
mingw32 mingw-w64-i686-gcc-fortran 10.2.0-1
mingw32 mingw-w64-i686-gcc-libgfortran 10.2.0-1
mingw32 mingw-w64-i686-gcc-libs 10.2.0-1 [已安装]
mingw32 mingw-w64-i686-gcc-objc 10.2.0-1
mingw64 mingw-w64-x86_64-gcc 10.2.0-1 [已安装]
mingw64 mingw-w64-x86_64-gcc-ada 10.2.0-1
mingw64 mingw-w64-x86_64-gcc-fortran 10.2.0-1
mingw64 mingw-w64-x86_64-gcc-libgfortran 10.2.0-1
mingw64 mingw-w64-x86_64-gcc-libs 10.2.0-1 [已安装]
mingw64 mingw-w64-x86_64-gcc-objc 10.2.0-1
msys gcc 9.3.0-1 [已安装]
msys gcc-fortran 9.3.0-1
msys gcc-libs 9.3.0-1 [已安装]
msys mingw-w64-cross-gcc 9.3.0-1
```
- 安装mingw32环境的gcc
``` nginx
pacman -S mingw-w64-i686-gcc
```
安装在 D:\msys64\mingw32
- 安装mingw64环境的gcc
``` nginx
pacman -S mingw-w64-x86_64-gcc
```
安装在 D:\msys64\mingw64
- 安装msys环境的gcc
``` nginx
pacman -S gcc 
```


## 编译ffmpeg
特别说明：用Msys2生成的库有依赖，依赖于D:\msys64\mingw32\bin 或 D:\msys64\mingw64\bin 下的某些dll库

### 编译32位库
1. 启动win32的cmd工具
2. 进入msys2的安装目录D:\msys64
``` stata
cd /d D:\msys64
```
3. 启动mingw32环境的msys2
``` stylus
msys2_shell.cmd -mingw32
```
4. 切换到ffmpeg的源码目录
``` gradle
cd /home/dell/src/ffmpeg-4.3.1/
```
5. 配置编译环境
``` brainfuck
 ./configure  --enable-shared --enable-static --arch=x86_32  --prefix=/usr/local/ffmpeg
```
6. `make`
7. `make install`
make install之后会在--prefix=/usr/local/ffmpeg参数指定的路径下/usr/local/ffmpeg下找到生成的头文件，lib以及dll

### 编译64位库
1. 启动win32的cmd工具
2. 进入msys2的安装目录D:\msys64
``` stata
cd /d D:\msys64
```
3. 启动mingw64环境的msys2
``` stylus
msys2_shell.cmd -mingw64
```
4. 切换到ffmpeg的源码目录
``` gradle
cd /home/dell/src/ffmpeg-4.3.1/
```
5. 配置编译环境
``` brainfuck
 ./configure  --enable-shared --enable-static --arch=x86_64  --prefix=/usr/local/ffmpeg
```
6. `make`
7. `make install`
make install之后会在--prefix=/usr/local/ffmpeg参数指定的路径下/usr/local/ffmpeg下找到生成的头文件，lib以及dll




