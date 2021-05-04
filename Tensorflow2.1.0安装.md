---
title: tensorflow2.1.0安装 
date: 2020-06-01 08:49:48
categories: 
- tensorflow2
tags:
- tensorflow2
- Python3
---

记录tensorflow安装以及环境测试

<!-- more -->

## 下载安装 python3.7 X64

 - python3.7.7在官网下载最新版本
 - 安装时注意将path添加到环境变量

## 下载安装 tensorflow-2.1.0

tensorflow的离线whl安装文件下载：[https://pypi.tuna.tsinghua.edu.cn/simple/tensorflow/][1]
  
 
## pip设置默认镜像

### 国内源

 清华：[https://pypi.tuna.tsinghua.edu.cn/simple][2]
 阿里云：[http://mirrors.aliyun.com/pypi/simple/][3]
 中国科技大学：[ https://pypi.mirrors.ustc.edu.cn/simple/][4]
 华中理工大学：[http://pypi.hustunique.com/][5]
 山东理工大学：[http://pypi.sdutlinux.org/ ][6]
 豆瓣：[http://pypi.douban.com/simple/][7]

1. 临时使用：
`pip install -i http://mirrors.aliyun.com/pypi/simple/`
  
2. 永久修改
 在C:\Users\{用户名}\目录下新建目录 `pip` ，在pip下新建 `pip.ini`,使用utf8无bom编码
 在`pip.ini`中添加如下配置
``` ini
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host=mirrors.aliyun.com
```

## tensorflow安装
 - tensorflow-2.1.0-cp37-cp37m-win_amd64.whl
 - 管理员运行cmd，切换到whl目录执行 `pip3  install tensorflow-2.1.0-cp37-cp37m-win_amd64.whl`

## tensorflow测试
``` python
import tensorflow as tf
from tensorflow.keras import layers
print(tf.__version__)
print(tf.keras.__version__)
```
2.1.0
2.2.4-tf


  [1]: https://pypi.tuna.tsinghua.edu.cn/simple/tensorflow/
  [2]: https://pypi.tuna.tsinghua.edu.cn/simple
  [3]: http://mirrors.aliyun.com/pypi/simple/
  [4]: https://pypi.mirrors.ustc.edu.cn/simple/
  [5]: http://pypi.hustunique.com/
  [6]: http://pypi.sdutlinux.org/
  [7]: http://pypi.douban.com/simple/
