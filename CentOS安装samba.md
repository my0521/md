---
title: CentOS安装samba
date: 2020-05-05 20:00:48
categories: 
- CentOS
tags:
- CentOS
---




## 安装
1. 安装
``` sql
yum -y install samba*
```
2. 关闭防火墙
``` stylus
systemctl stop firewalld.service
systemctl disable firewalld.service 
```
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
3. 关闭selinux
``` gradle
cat /etc/selinux/config 
```
SELINUX=disabled

## 配置
1. 添加[share]节点 `vim /etc/samba/smb.conf`
``` nix
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
	workgroup = SAMBA
	security = user

	passdb backend = tdbsam

	printing = cups
	printcap name = cups
	load printers = yes
	cups options = raw
[share]
	comment = Public stuff
	guest ok = Yes
	path = /usr/local/share
	read only = No
	writeable = yes
	public = yes 

[homes]
	comment = Home Directories
	valid users = %S, %D%w%S
	browseable = No
	read only = No
	inherit acls = Yes

[printers]
	comment = All Printers
	path = /var/tmp
	printable = Yes
	create mask = 0600
	browseable = No

[print$]
	comment = Printer Drivers
	path = /var/lib/samba/drivers
	write list = @printadmin root
	force group = @printadmin
	create mask = 0664
	directory mask = 0775
```
2. 创建用户
``` gradle
useradd -s /sbin/nologin admin
```
3. 设置admin密码
``` stylus
smbpasswd -a admin
```
4. 为共享目录添加用户权限
``` gradle
useradd -g admin -s /sbin/nologin /usr/local/share
```

## smb共享
1. 启动
``` sql
systemctl start smb
```
2. 查看smb服务启动状态
``` puppet
service smb status
```


## windows访问
1. 打开我的电脑
2. 打开映射网诺驱动器
3. 输入 \\10.27.100.158\share
4. 输入 admin 及登录密码认证






