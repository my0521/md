---
title: Python 多路复用
date: 2020-07-01 20:00:48
categories: 
-  Python3
tags:
-  Python3
---

Python3多路复用
<!-- more -->

# 原理

``` javascript
'''
IO模型:
1. 上层IO操作的过程: 内核态等待数据(wait_data) + 内核态将数据拷贝给用户态程序(copy_data)
2. 同步阻塞: wait_data(block) + copy_data(block)
 1) 定义: 
 即用户态向内核态请求数据, 
 若没有, 该线程(进程)直接就被挂起了(也就是当前程序阻塞了), 
 直到数据来了该线程才被激活就绪
 
 2) 解决方法: 
 #1. mulit-threading --> 费资源
 #2. pool --> 池子大小设计 
 
 3) 常见的同步阻塞:
 #1. socket + TCP: s.accept()/connect_ex(), conn.recv()/conn.send()
 #2. socket + UDP: s.recvfrom()
 #3. 文件: read()/write() 
''' # IO模型: 同步阻塞

'''
同步非阻塞: wait_data(unblock, 轮询) + copy_data(block)
 1) 定义: 
 即用户态向内核请求数据, 
 若没有, 向该线程(进程)返回error; 该线程可在下次询问之前做其他的事
 若有, 则向该线程用户态拷贝数据(block)
 
 2) 使用:
 #1. server.setblocking(False) # 声明为非阻塞型服务器
 
 #2. wait_data不到:
 server.accept()
 conn.recv()
 conn.send()
 raise BlockingIOError:
 期间可做其他事情
 
 #3. 定义几个数据集用以处理请求:
 req_list: 当有请求接入, 存入
 del_list: 将无数据传入的连接存入, 之后把所有废弃请求从req_list中删除
 w_dict: 将有数据传入的连接存入, 之后统一处理
''' # IO模型: 同步非阻塞

'''
同步IO复用: select[conn1,conn2,...](block) + [conn_i]copy_data(block)
 1) 定义:
 利用select同时检测多个non-blocking连接, 哪个wait_data(ok)就让其copy_data
 
 2) 特点:
 #1. 相当于还是两次block(select + copy_data), 而且还有两次系统调用(select + recv)
 #2. 适用于大规模连接的情况, 小规模还是mulit-threading + blocking IO
 
 3) select原理:
 #1. select采用无差别轮询的方法
 #2. 通过水平触发的方式(只有内核态有数据就会触发)
 #3. 检测其用户态文件描述符(fd)的变化, 从而找到其对应内核态的数据
 #4. 允许其copy_data到用户态
 #5. 时间复杂度为O(n)

 4) poll: 
 相比select取消了文件描述符的数量限制, select是1024
 
 5) epoll: 
 #1. 相比select采用边缘触发方式检测, 即当内核态数据发生变化(有/无)才会触发检测
 #2. 采用问答式检测, 无需轮询 
 
* select使用:
 # 初始化
 r_list = [server, ]
 w_list = []
 e_list = []
 
 # select检测
 r_able, w_able, e_able = select.select(r_list, w_list, e_list,[timeout])
 r_able: 检测到的可读连接list
 w_able: 检测到的可写连接list
 e_able: 检测到的发生异常的连接list
 timeout: select
```


## Server

### Select Poll

``` javascript

import select
import socket
# 创建套接字
sk = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
# 可重复绑定
sk.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
# 绑定本机地址端口
sk.bind(('', 8080))
# 变为被动服务器
sk.listen(32)

sk.setblocking(False)

rlist = list([sk, ])

wlist = list()

xlist = list()

wdict = dict()

while 1:
    rl, wl, xl = select.select(rlist, wlist, xlist, None)
    for sock in rl:
        if sock == sk:
            conn, addr = sock.accept()
            rlist.append(conn)
        else:
            try:
                recvs = sock.recv(1024)
                if not recvs:
                    sock.close()
                    rlist.remove(sock)
                print(recvs.decode(encoding='utf-8'))
                wlist.append(sock)
                wdict[sock] = recvs + b'_suffix'
            except ConnectionResetError:
                sock.close()
                rlist.remove(sock)

    for sock in wl:
        sock.sendall(wdict.get(sock))
        wdict.pop(sock)
        wlist.remove(sock)
```

###  epoll
- 没有最大并发连接的限制，能打开的FD(指的是文件描述符，通俗的理解就是套接字对应的数字编号)的上限远大于1024
- 效率提升，不是轮询的方式，不会随着FD数目的增加效率下降。只有活跃可用的FD才会调用callback函数；即epoll最大的优点就在于它只管你“活跃”的连接，而跟连接总数无关，因此在实际的网络环境中，epoll的效率就会远远高于select和poll。

``` javascript
import socket
import select

# 创建套接字
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 可重复绑定
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

# 绑定本机地址端口
s.bind(("", 7788))

# 变为被动服务器
s.listen(1024)

# 创建一个epoll对象
epoll = select.epoll()

# 在epoll中注册s套接字(注意此处不是直接用是s, 而是使用s的fileno)
epoll.register(s.fileno(), select.EPOLLIN|select.EPOLLET)


# 创建两个字典, 来保存fileno和与其对应的套接字和地址
connections = {}
addresses = {}

# 开始等待客户端发送来的信息
while True:
    
    # 对epoll中的套接字进行扫描
    epollList = epoll.poll()

    # 对扫描到的事件进行判断
    for fd,events in epollList:
        
        # 如果判断是s套接字
        if fd == s.fileno():
            conn,addr = s.accept()

            print("有新的客户端到来...%s"%str(addr))
            connections[conn.fileno()] = conn
            addresses[conn.fileno()] = addr

            epoll.register(conn.fileno(), selecte.EPOLLIN|select.EPOLLET)

        # 如果是接收到了数据
        elif events == select.EPOLLIN:
            recvData = connections[fd].recv(1024)

            if len(recvData) > 0:
                print("recvData: %s"%recvData)

            else:
                epoll.unregister(fd)
                connections[fd].close()

                print("%s....offline....."%str(addresses[fd]))
```



###  client

``` javascript

import socket
import threading


def my_client():
    sk = socket.socket(family=socket.AF_INET, type=socket.SOCK_STREAM)
    sk.connect(('localhost', 8080))
    while 1:
        sk.send(b'hi')
        msg = sk.recv(1024)
        print(msg)


for i in range(10):
    threading.Thread(target=my_client).start()

```