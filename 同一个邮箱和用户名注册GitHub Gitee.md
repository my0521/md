---
title: 同一个邮箱和用户名注册GitHub Gitee
date: 2019-05-04 18:49:48
categories:
- git
tags:
- git
---

使用同一个邮箱和用户名注册GitHub与Gitee方法介绍

<!-- more -->

## 公钥问题及解决
``` javascript
//Gitee SSH-key生成命令
$ ssh-keygen -t rsa -C 'xxxxx@youdomian.com' -f ~/.ssh/github_id_rsa
//GitHub SSH-key生成命令
$ ssh-keygen -t rsa -C 'xxxxx@youdomian.com' -f ~/.ssh/gitee_id_rsa
```

## 共用同一个本地仓库
### 关联命令
``` stylus
//Gitee
$ git remote add gitee git@gitee.com:xxx/xxx.git
//GitHub
$ git remote add gitHub git@github.com:xxx/xxx.git
```

### 推送命令
``` gradle
//github
$ git push github master
//Gitee
$ git push gitee master
```
## 项目迁移
gitee导入地址：[https://gitee.com/projects/import/url][1]


  [1]: https://gitee.com/projects/import/url

