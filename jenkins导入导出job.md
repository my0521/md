---
title: jenkins导入导出job
date: 2021-03-01 20:00:48
categories: 
-  jenkins
tags:
-  jenkins
---

jenkins导入导出job
<!-- more -->


# 下载jenkins-cli.jar
下载位置：登陆Jenkins--首页--Manage Jenkins-- Jenkins CLI--download

# 导出job：

java -jar jenkins-cli.jar -s http://url-server get-job autotest > autotest .xml

# 导入job：

java -jar jenkins-cli.jar -s http://url-server create-job autotest < autotest.xml

# 使用指定账户

 - -auth 用户:密码
  java -jar jenkins-cli.jar -s http://url-server  -auth test:test get-job autotest > autotest .xml