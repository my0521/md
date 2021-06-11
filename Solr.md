---
title: Solr
date: 2020-07-01 20:00:48
categories: 
-  Solr
tags:
-  Solr
---

# 安装

 - 下载   [http://archive.apache.org/dist/lucene/solr/](http://archive.apache.org/dist/lucene/solr/)
 - 启动：    进入bin， `solr start`
 - 访问   [http://localhost:8983/solr/](http://localhost:8983/solr/)

# 新建实例
  ## 进入bin目录
- 启动：bin\solr.cmd start
- 停止：bin\solr stop 或bin\solr stop -all
- 查看：bin\solr status
 
### 新建`collection`实例

``` n1ql
   solr create -c mdb
```
实例存放在`server/solr`目录下

# 导入数据
导入数据前需copy jar包
1. data-config.xml
``` javascript
<?xml version="1.0" encoding="UTF-8" ?>
<dataConfig> 
	<dataSource type="JdbcDataSource" 
				driver="com.mysql.cj.jdbc.Driver" 
				url="jdbc:mysql://localhost:3306/test?serverTimezone=UTC"  
				user="root" 
				password="wrhkw666"/> 
	<document> 
		<entity name="Student"  query="select uid,s_name,address from student"> 
			<field column="uid" name="id" /> 
			<field column="s_name" name="s_name" /> 
			<field column="address" name="s_address" /> 
		</entity>
	</document>
</dataConfig>
```
2. solrconfig.xml 

``` javascript
  <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler"> 
        <lst name="defaults">  
              <str name="config">data-config.xml</str>  
        </lst>  
    </requestHandler> 
```
3. managed-schema 

``` javascript
   <field name="s_name" type="string" indexed="true" stored="true"/>
    <field name="s_address" type="string" indexed="true" stored="true"/>
```
导入数据失败可以查看solr的log文件，里面记录有异常信息




