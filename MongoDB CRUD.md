---
title: MongoDB CRUD
date: 2020-07-01 20:00:48
categories: 
- MongoDB
tags:
- MongoDB
- SpringBoot
---
-
记录SpringBoot集成MongoDB使用JPA进行数据访问
<!-- more -->

客户端工具下载：[https://nosqlbooster.com/downloads][1]

## MongoDB 优缺点
1. 优点
- 文档结构的存储方式，能够更便捷的获取数据
- 内置GridFS，支持大容量的存储
  GridFS是一个出色的分布式文件系统，可以支持海量的数据存储。内置了GridFS了MongoDB，能够满足对大数据集的快速范围查询。
- 海量数据下，性能优越
- 动态查询
- 全索引支持,扩展到内部对象和内嵌数组
- 查询记录分析
- 快速,就地更新
- 高效存储二进制大对象 (比如照片和视频)
- 复制（复制集）和支持自动故障恢复
- 内置 Auto- Sharding 自动分片支持云级扩展性，分片简单
- MapReduce 支持复杂聚合
- 商业支持,培训和咨询
2. 缺点
- 不支持事务操作
所以事务要求严格的系统（如果银行系统）肯定不能用它
- MongoDB 占用空间过大 （不过这个确定对于目前快速下跌的硬盘价格来说，也不算什么缺点了）
- MongoDB没有如MySQL那样成熟的维护工具
- 无法进行关联表查询，不适用于关系多的数据
- 复杂聚合操作通过mapreduce创建，速度慢
- 模式自由,自由灵活的文件存储格式带来的数据错
- MongoDB 在你删除记录后不会在文件系统回收空间。除非你删掉数据库。但是空间没有被浪费

## SpringBoot集成MongoDB
1. 添加pom依赖
``` vbscript-html
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-mongodb</artifactId>
	</dependency> 
```
2. mongodb配置
``` haskell
spring.data.mongodb.host=127.0.0.1
spring.data.mongodb.port=27017
spring.data.mongodb.database=mytest
# 默认没有账号密码
spring.data.mongodb.username=mytest
spring.data.mongodb.password=666666
```
3. 定义实体类
``` java

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection="book")
public class Book {
	@Id
	private String id;
	private String bookName;
	private String from;
	private String price;
	private String author;
	//...其他属性
	//getter setter
}
```
4. Repository接口
``` java
import org.springframework.data.mongodb.repository.MongoRepository;

import com.my.beans.Book;

public interface BookRepository extends MongoRepository<Book, String> {

	public Book findByAuthor(String author);

}

```
5.  BookService
``` java
import javax.annotation.Resource;

import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;
import org.springframework.stereotype.Service;

import com.my.beans.Book;
import com.my.repository.BookRepository;

@Service
public class BookService {
	@Resource
	private  BookRepository bookRepository;
	public void Save(Book book) {
		bookRepository.save(book);
	}
	public void insert(Book book) {
		bookRepository.insert(book);
	}
	public void update(String id)
	{
		Book book = bookRepository.findById(id).get();
		book.setAuthor("小黄");
		bookRepository.save(book);
	}
	public void deleteById(String id)
	{
		bookRepository.deleteById(id);
	}
	/**
	 * 其实也是根据id来删除的
	 * @param id
	 */
	public void deleteByEntity(String id)
	{
		Book book = bookRepository.findById(id).get();
		bookRepository.delete(book);
	}
	/**
	  * 利用ExampleMatcher 自定义条件来获取书本
	 * @param book
	 * @return
	 */
	public Book getByOne(Book book)
	{
		ExampleMatcher matcher = ExampleMatcher.matching().withIgnorePaths("bookName", "author");
		Example<Book> example = Example.of(book, matcher);
		return bookRepository.findOne(example).get();

	}	
	public Book getByAuthor(String author)
	{		
		return bookRepository.findByAuthor(author);
	}
}
```
6.  BookController
``` java
import javax.annotation.Resource;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import com.my.beans.Book;
import com.my.service.BookService;
@RestController
public class BookController
{
	@Resource
	private BookService bookService;	
	/**
	 * 保存方法1
	 * @url http://localhost:8038/save
	 * @return
	 */
	@GetMapping(value = "/save")
	public String save()
	{
		try{
			Book book = null;
			for(int i=0;i<100;i++) {	
				book = new Book();
				book.setAuthor("小明"+i);
				book.setBookName("从你在世界走过"+i);
				book.setPrice(i+"29.9");
				book.setFrom("清华出版"+i);
				bookService.Save(book);
			}
			
			return "success";
		}catch(Exception e){
			return "fail";
		}
	}
	/**
	 * 保存方法2
	 * @url http://localhost:8038/insert
	 * @return
	 */
	@GetMapping(value = "/insert")
	public String insert()
	{
		try{
			Book book = new Book();
			book.setAuthor("小明2");
			book.setBookName("从你在世界走过2");
			book.setPrice("29.9");
			book.setFrom("清华出版2");
			bookService.Save(book);
			return "success";
		}catch(Exception e){
			return "fail";
		}
	}
	/**
	 * 更新方法
	 * @url http://localhost:8038/update/5d174b38a8c079f248178182
	 * @return
	 */
	@GetMapping(value = "/update/{id}")
	public String update(@PathVariable("id") String id)
	{
		try{
			// 例如id  5d174b38a8c079f248178182 
			bookService.update(id);
			return "success";
		}catch(Exception e){
			return "fail";
		}
	}
	/**
	 * 根据id来删除文档
	 * @url http://localhost:8038/deleteByid/5d174b38a8c079f248178182
	 * @return
	 */
	@GetMapping(value = "/deleteByid/{id}")
	public String deleteByid(@PathVariable("id") String id)
	{
		try{
			bookService.deleteById(id);
			return "success";
		}catch(Exception e){
			return "fail";
		}
	}	
	/**
	 * 根据实体文档来删除
	 * @url http://localhost:8038/deleteByentity/5d17a867a8c079ed247c54bd
	 * @return
	 */
	@GetMapping(value = "/deleteByentity/{id}")
	public String deleteByentity(@PathVariable("id") String id)
	{
		try{
			bookService.deleteByEntity(id);
			return "success";
		}catch(Exception e){
			return "fail";
		}
	}
	
	/**
	 * 用Example的方法来查询
	 * @uri http://localhost:8038/get/小明
	 * @param name
	 * @return
	 */
	@GetMapping(value = "/get/{name}")
	public Book getOne(@PathVariable("name") String name)
	{
		//可以把你要查询的字段任意设置为某一个字段的值，
		Book book = new Book();
		book.setBookName(name);
		
		return bookService.getByOne(book);
	}
	
	/**
	 * 根据jpa的命名方式来查询
	 * @param author
	 * @return
	 */
	@GetMapping(value = "/getByauthor/{author}")
	public Book getByAuthor(@PathVariable("author") String author)
	{
		return bookService.getByAuthor(author);
	}
}
```
7. 启动SpringBoot，访问接口http://192.168.1.1/getByauthor/{author}

## 错误记录
1. `too many users are authenticated`
解决方案：先退出命令行，重新进入，不要多次db.auth(...)



  [1]: https://nosqlbooster.com/downloads

