---
title: MySql查询缓存
date: 2019-05-07 20:00:48
categories: 
 - MySql
tags:
 - MySql
---

查询缓存以及自动选择不使用查询缓存的情况。

<!-- more -->

MySql查询缓存(5.1.17开始支持)顾名思义，只是针对查询的缓存功能。在WEB开发甚至社会各种生活的方方面面，都是缓存为王。MYSQL层也不例外，MYSQL查询缓存功能缓存的是SELECT操作或预处理查询的SQL语句和结果集；新的SELECT语句或预处理查询语句，先去查询缓存(根据整条SQL)，判断是否存在可用的记录集，注意SQL语句必须是完全一样，SQL有改变大小写或者加上某个空格也会导致查询缓存不可用。即使完全相同的SQL，如果使用不同的字符集、不同的协议等也会被认为是不同的查询而分别进行缓存。

## MySql查询缓存的启用
1. 启用mysql查询缓存涉及两个配置：`query_cache_type`和`query_cache_size`，任何一个参数设置为0都是查询缓存功能不可用。如果确实要关闭查询缓存，请设置query_cache_type为0，可减少检查query_cache_size的配置。

2. query_cache_type: 有0、1、2三个取值
- 0则不使用查询缓存。
- 1表示始终使用查询缓存。
- 2表示按需使用查询缓存。
3. 如果query_cache_type为1而又不想利用查询缓存中的数据，可以用下面的SQL： 
    SELECT SQL_NO_CACHE * FROM my_table WHERE condition;
    如果值为2，要使用缓存的话，需要使用SQL_CACHE开关参数：
    SELECT SQL_CACHE * FROM my_table WHERE condition;

4. 可以通过命令查看查询缓存命中次数： SHOW STATUS LIKE 'Qcache_hits';
- query_cache_size:允许设置query_cache_size的值最小为40K，对于最大值则可以几乎认为无限制，实际生产环境的应用经验告诉我们，该值并不是越大，查询缓存的命中率就越高，也不是对服务器负载下降贡献大，反而可能抵消其带来的好处，甚至增加服务器的负载，至于该如何设置，下面的章节讲述，推荐设置 为：64M；
- query_cache_limit:限制查询缓存区最大能缓存的查询记录集，可以避免一个大的查询记录集占去大量的内存区域，而且往往小查询记录集是最有效的缓存记录集，默认设置为1M，建议修改为16k~1024k之间的值域，不过最重要的是根据自己应用的实际情况进行分析、预估来设置；
- query_cache_min_res_unit:设置查询缓存分配内存的最小单位，要适当地设置此参数，可以做到为减少内存块的申请和分配次数，但是设置过大可能导致内存碎片数值上升。默认值为4K，建议设置为1k~16K
- query_cache_wlock_invalidate:该参数主要涉及MyISAM引擎，若一个客户端对某表加了写锁，其他客户端发起的查询请求，且查询语句有对应的查询缓存记录，是否允许直接读取查询缓存的记录集信息，还是等待写锁的释放。默认设置为0，也即允许；
在表的结构或数据发生改变时，查询缓存中的数据不再有效。比如INSERT、UPDATE、 DELETE、TRUNCATE、ALTER TABLE、DROP TABLE或DROP DATABASE会导致缓存数据失效。所以查询缓存适合有大量相同查询的应用，不适合有大量数据更新的应用。对于一些并不是要经常查询的数据库，可以使用query_cache_type=2模式，然后SQL语句加SQL_CACHE参数指定，以减少查询缓存的开销。

## 缓存失效
但即便开启了MYSQL查询缓存，但在以及条件下MYSQL会自动选择不使用查询缓存：查询缓存对什么样的查询语句，无法缓存其记录集，大致有以下几类：
- 查询语句中加了SQL_NO_CACHE参数；
- 查询语句中含有获得值的函数，包涵自定义函数，如：CURDATE()、GET_LOCK()、RAND()、CONVERT_TZ等；
- 对系统数据库的查询：mysql、information_schema
- 查询语句中使用SESSION级别变量或存储过程中的局部变量；
- 查询语句中使用了LOCK  IN SHARE MODE、FOR UPDATE的语句
- 查询语句中类似SELECT …INTO 导出数据的语句；
- 事务隔离级别为：Serializable情况下，所有查询语句都不能缓存；
- 对临时表的查询操作；
- 存在警告信息的查询语句；例见:http://www.04007.cn/article/343.html
- 不涉及任何表或视图的查询语句；
- 某用户只有列级别权限的查询语句；

## 查询缓存区的碎片整理
查询缓存使用一段时间之后，都会出现内存碎片，为此需要监控相关状态值，并且定期进行内存碎片的整理和清理维护
- FLUSH QUERY CACHE; //清理查询缓存内存碎片。
- RESET QUERY CACHE; //从查询缓存中移出所有查询。
- FLUSH TABLES; //关闭所有打开的表，同时该操作将会清空查询缓存中的内容。
