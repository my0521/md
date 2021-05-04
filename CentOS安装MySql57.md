---
title: CentOS7安装MySql5.7 
date: 2017-01-01 20:00:48
categories: 
 - MySql
tags:
 - MySql
 - CentOS
---
记录在CentOS下安装MySQL的步骤

<!-- more -->

## 安装MySql

- 下载MySql官方的 Yum Repository
``` stylus
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```
使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了

- 安装Yum Repository
``` stylus
yum -y install mysql57-community-release-el7-10.noarch.rpm
```

- 安装MySql服务器
``` sql
yum -y install mysql-community-server
```

## 管理MySql
- 启动MySql
``` puppet
systemctl start  mysqld.service
systemctl restart mysqld.service
```

- 查看MySql运行状态
``` puppet
systemctl status mysqld.service
```

- 获取MySql临时密码
``` stata
grep "password" /var/log/mysqld.log
```

- 使用临时密码进入数据库
``` nginx
mysql -uroot -p
```

- 修改默认密码
``` sql
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'esZF<oJjj83H';
```

- 开启MySql远程访问
``` sql
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
FLUSH PRIVILEGES;
```

- 开启开机自启动
``` smali
systemctl enable mysqld
systemctl daemon-reload
```

- 设置MySQL的字符集为UTF-8，令其支持中文`vim /etc/my.cnf`

	``` sql
	# For advice on how to change settings please see
	# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html
	 
	[mysql]
	default-character-set=utf8
	 
	[mysqld]
	datadir=/var/lib/mysql
	socket=/var/lib/mysql/mysql.sock
	default-storage-engine=INNODB
	character_set_server=utf8
	 
	symbolic-links=0
	 
	log-error=/var/log/mysqld.log
	pid-file=/var/run/mysqld/mysqld.pid
	```

- 卸载MySQL仓库
``` vim
rpm -qa | grep mysql
yum -y remove mysql57-community-release-el7-10.noarch
```








