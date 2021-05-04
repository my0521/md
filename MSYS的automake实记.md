---
title: MSYS的automake实记 
date: 2018-01-01 20:00:48
categories: 
- make
tags:
- make
---

记录automake编译一个项目

<!-- more -->

## 先安装依赖组件
- autoconf
- automake，
- m4
- libtool
- perl
- crypt

## automake
1. 项目结构
``` stata
.
├── bin
├── build
├── include
│   └── test.h
└── src
    ├── main.cpp
    └── test.cpp
```
- touch Makefile.am
``` haskell
AUTOMAKE_OPTIONS=foreign
SUBDIRS=src  
```
该目录下面保存的是与 autotools 相关的文件，没有需要编译安装的文件，所以只注
明需要进一步处理的子目录信息`SUBDIRS=src`  
- touch Makefile.in
- touch src/Makefile.am
``` makefile
INCLUDES=-I$(top_srcdir)/include 
bin_PROGRAMS=$(top_srcdir)/bin/test
__top_srcdir__bin_test_SOURCES=test.cpp main.cpp  
```
bin_PROGRAMS  指明生成的可执行文件test
__top_srcdir__bin_test_SOURCES 列出生成可执行文件需要的源文件test.cpp main.cpp 
- touch src/Makefile.in

2. 生成m4 宏命令文件configure.scan
``` undefined
autoscan
```
3. 将configure.scan复制一份为configure.ac，并修改配置
configure.ac 文件是 autoconf 的输入文件，经过 autoconf 处理，展开里面的 m4 宏，
输出的是 configure 脚本
``` stylus
cp configure.scan configure.ac
```
- 修改配置
``` less
#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.69])
AC_INIT([test], [1.0], [811391195@qq.com]) #test是软件名称
AC_CONFIG_SRCDIR([src/main.cpp]) #src/main.cpp是主函数入口
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE #add

# Checks for programs.
AC_PROG_CXX
AC_PROG_CC

# Checks for libraries.

# Checks for header files.
AC_CHECK_HEADERS([stdlib.h])

# Checks for typedefs, structures, and compiler characteristics.

# Checks for library functions.

AC_CONFIG_FILES([Makefile src/Makefile]) ## add
AC_OUTPUT
```
	1. `AC_INIT` 宏用来定义软件的名称和版本等信息
	2. `AC_CONFIG_SRCDIR` 宏通过侦测所指定的源码文件是否存在，来确定源码目录的有效性可以选择源码目录中的任何一个文件作为代表
	3. `AC_CONFIG_HEADER` 宏用于生成 config.h 文件，里面存放 configure 脚本侦测到的信息如果程序需要使用其中的定义，就在源码中加入#include 
	4. configure.ac 文件要求 AC_INIT 宏必须放在开头位置，AC_OUTPUT 放在文件末，中间用来检测编译环境的各种宏没有特别的先后次序要求，由宏之间相互关系决定Makefile 文件的产生
	5. 简单的 Makefile.in 可以手动编写，如果使用 automake 产生，需要在 configure.ac 里面加入 AM_INIT_AUTOMAKE 宏进行声明
	6. 在 autotools 的命名习惯中，后缀 ac 的文件是 autoconf 的输入文件，后缀 am 的文件是 automake 的输入文件，后缀 in 的文件是 configure 的输入文件 autoconf 旧版本中 configure.in 等同于 configure.ac，虽然新版本也可以识别，但它不符合命名规则，所以新版本的文件应该使用 ac 后缀
	7.  configure.ac 里面的宏，主要作用是侦测系统，并没有编译相关的设置因为这些信息是写在 Makefile.am 里面，然后用 automake 工具转换成 Makefile.in，configure脚本执行时再读取 Makefile.in，并与侦测信息一起写到 Makefile 文件
	8. 要输出 Makefile，还需要在 configure.ac 中使用 AC_CONFIG_FILES 宏指明该宏并不
	是只处理 Makefile，而是将 FILE.in 文件转换为 FILE 文件因为 make 可以遍历子目
	录，如果子目录中存在 Makefile，也将同时处理在本例中 src 目录下是源码，其他是
	数据文件，可以使用单独一个 Makefile 放在根目录下面，也可以用多个 Makefile由于
	每个子目录的 Makefile 只处理本目录的文件，分工明确，是模块化的方法，推荐使用
	因此在 configure.ac 里面增加下面的宏，表示软件根目录和子目录中都需要生成
	Makefile 文件 `AC_CONFIG_FILES([Makefile src/Makefile])` 

4. aclocal(aclocal-1.10)
``` smali
#忽略，此提示网查说是perl语法问题
main::scan_file() called too early to check prototype at /usr/bin/aclocal-1.10 line 617.
```
5. autoconf
6. autoheader
 生成 config.h.in文件
7. automake --add-missing
automake-1.10 --add-missing
``` http
configure.ac:8: installing `./install-sh'
configure.ac:8: installing `./missing'
```
6. mkdir build && cd build
7.  ../configure
``` stylus
configure: loading site script /mingw32/etc/config.site
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a thread-safe mkdir -p... /usr/bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking for g++... g++
checking whether the C++ compiler works... yes
checking for C++ compiler default output file name... a.exe
checking for suffix of executables... .exe
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking for style of include used by make... GNU
checking dependency style of g++... gcc3
checking for gcc... gcc
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking dependency style of gcc... gcc3
checking how to run the C preprocessor... gcc -E
checking for grep that handles long lines and -e... /usr/bin/grep
checking for egrep... /usr/bin/grep -E
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking for stdlib.h... (cached) yes
configure: creating ./config.status
config.status: creating Makefile
config.status: creating src/Makefile
config.status: creating config.h
config.status: executing depfiles commands
```
8. make
``` stylus
make  all-recursive
make[1]: 进入目录“/home/dell/src/test/build”
Making all in src
make[2]: 进入目录“/home/dell/src/test/build/src”
g++ -DHAVE_CONFIG_H -I. -I../../src -I.. -I../../include     -g -O2 -MT test.o -MD -MP -MF .deps/test.Tpo -c -o test.o ../../src/test.cpp
mv -f .deps/test.Tpo .deps/test.Po
g++ -DHAVE_CONFIG_H -I. -I../../src -I.. -I../../include     -g -O2 -MT main.o -MD -MP -MF .deps/main.Tpo -c -o main.o ../../src/main.cpp
mv -f .deps/main.Tpo .deps/main.Po
g++  -g -O2   -o ../../bin/test.exe test.o main.o
make[2]: 离开目录“/home/dell/src/test/build/src”
make[2]: 进入目录“/home/dell/src/test/build”
make[2]: 离开目录“/home/dell/src/test/build”
make[1]: 离开目录“/home/dell/src/test/build”
```
9. ../bin/test.exe
``` erlang
请按任意键继续. . .

hello 5
```









