---
title: Python 知识点
date: 2020-07-01 20:00:48
categories: 
-  Python3
tags:
-  Python3
---

<!-- more -->

#  `__new__`与  `__ init __`
1. __init__是初始化方法，创建对象后，就立刻被默认调用了，可接收参数
2. __new__至少要有一个参数 cls，代表当前类，此参数在实例化时由 Python 解释器自动识
别
3. __new__必须要有返回值，返回实例化出来的实例，这点在自己实现__new__时要特别注
意，可以 return 父类（通过 super(当前类名, cls)）__new__出来的实例，或者直接是 object
的__new__出来的实例
4. __init__有一个参数 self，就是这个__new__返回的实例，__init__在__new__的基础上可
以完成一些其它初始化的动作，__init__不需要返回值
5. 如果__new__创建的是当前类的实例，会自动调用__init__函数，通过 return 语句里面调
用的__new__函数的第一个参数是 cls 来保证是当前类实例，如果是其他类的类名，；那么实
际创建返回的就是其他类的实例，其实就不会调用当前类的__init__函数，也不会调用其他
类的__init__函数

``` javascript
class  A:
    def __init__(self, name, color):
        self.name = name
        self.color = color
        print("__init__  A")

    def test(self):
        print("test A")

    def __new__(cls, *args, **kwargs):
        print("__new__ ")
        # return object.__new__(cls)
        return object.__new__(B)

class B:
    def __init__(self, name, color):
        self.name = name
        self.color = color
        print("__init__  B")

    def test(self):
        print("test B")

```

结果：

``` subunit
# __new__
# __init__  A
# test A
__new__ 
test B
```

# 字典
1. 排序   

``` javascript
    dict = {"name": "my", "age": 20, "city": "上海","tel":"13600000000"}
    dict = sorted(dict.items(),key=lambda i:i[0])
```
结果：

``` javascript
{'name': 'my', 'age': 20, 'city': '上海', 'tel': '13600000000'}
[('age', 20), ('city', '上海'), ('name', 'my'), ('tel', '13600000000')]

```

2. 合并 

``` javascript
dict1.update(dict2)
```

# re
1. 字符串中提取值 ，提取值用()包裹
``` javascript
    str ='<div class="nam">中国</div>'
    res = re.findall(r'<div class=".*">(.*?)</div>', str)
    print(res)
```
输出: ['中国']
#（.*）是贪婪匹配，会把满足正则的尽可能多的往后匹配
#（.*?）是非贪婪匹配，会把满足正则的尽可能少匹配

2. 匹配数字 和英文
``` javascript
a = "not 404 found 张三 99 深圳"
    li = a.split(" ")
    print(li)
    res = re.findall('\d+|[a-zA-Z]+', a)  # \d 匹配数字，|[]中匹配单词，+连接多个匹配方式
    for i in res:
        if i in li:
            li.remove(i)
    new_str = " ".join(li)
    print(res)
    print(new_str)
```
输出：

``` javascript
['not', '404', 'found', '张三', '99', '深圳']
['not', '404', 'found', '99']
张三 深圳
```

 3. 替换

  

``` javascript
    a = "张明 98 分"
    ret = re.sub(r"\d+", "100", a)
    print(ret)
```

# 多线程

``` vim
import threading
from time import sleep,ctime

def talk(content, count):
    for i in range(count):
        print(f'start to talk{content, ctime()}')
    sleep(3)

def write(content,count):
    for i in range(count):
        print(f'start to write{content, ctime()}')
    sleep(2)
threads = []

if __name__ == '__main__':
    t1 = threading.Thread(target=talk, args=('hello world', 10))
    threads.append(t1)
    t2 = threading.Thread(target=write, args=('oh my god', 10))
    threads.append(t2)

    for thread in threads:
        thread.start()
    for thread in threads:
        thread.join()  # join 方法作用是主线程等待调用 join 方法的子线 程终止后再往下执行
```




