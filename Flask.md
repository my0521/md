---
title: Flask
date: 2020-07-01 20:00:48
categories: 
-  Flask
tags:
-  Flask
---

Flask 应用创建
<!-- more -->


# 安装flask库

``` ebnf
python -m pip install  flask
```

# 一个最小的应用

``` python
from flask import Flask
app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run()
```


# 命令行启动

 - set FLASK_APP=main.py
 - python -m flask run



## 参考
[https://dormousehole.readthedocs.io/en/latest/](https://dormousehole.readthedocs.io/en/latest/)