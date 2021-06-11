---
title: Lucene全文检索
date: 2020-07-01 20:00:48
categories: 
-  Lucene
tags:
-  Lucene
---

使用Lucene的API来实现对索引的增（创建索引）、删（删除索引）、改（修改索引）、查（搜索数据）
<!-- more -->
- Lucene：底层的API，工具包
- Solr：基于Lucene开发的企业级的搜索引擎产品
- Elasticsearch：基于Lucene开发的企业级的搜索引擎产品
 参考[https://www.cnblogs.com/leeSmall/p/9027172.html](https://www.cnblogs.com/leeSmall/p/9027172.html)

# 依赖包

``` javascript
    <properties>
		<java.version>1.8</java.version>
		<maven-jar-plugin.version>3.1.1</maven-jar-plugin.version>
		<lunece.version>8.5.0</lunece.version>
	</properties>
```

``` javascript
		<!-- lucene核心库 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- Lucene的查询解析器 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-queryparser</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- lucene的默认分词器库 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-analyzers-common</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        
        <!--  lucene-queryparser 查询分析器模块 -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-queryparser</artifactId>
            <version>${lunece.version}</version>
        </dependency>
        <!-- ik分词器 -->
        <dependency>
            <groupId>com.jianggujin</groupId>
            <artifactId>IKAnalyzer-lucene</artifactId>
            <version>8.0.0</version>
        </dependency>
        
         <!-- 文件IO操作 -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
```
# 创建索引

``` javascript
//创建索引
    @Test
    public void luceneCreateIndex() throws Exception{
    	String path_ = "src/main/resources/Lucene/Document";
    	String index_ = "src/main/resources/Lucene/index";
    	System.out.println(path_);
    	System.out.println(index_);
        //指定索引存放的位置
        //E:\Lucene_index
        Directory directory = FSDirectory.open(Paths.get(new File(index_).getPath()));
        System.out.println("pathname"+Paths.get(new File(index_).getPath()));
       //创建一个分词器
//        StandardAnalyzer analyzer = new StandardAnalyzer();
//        CJKAnalyzer cjkAnalyzer = new CJKAnalyzer();
        Analyzer analyzer =new IKAnalyzer();
        //创建indexwriterConfig(参数分词器)
        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(analyzer);
        //创建indexwrite 对象(文件对象，索引配置对象)
        IndexWriter indexWriter = new IndexWriter(directory,indexWriterConfig);
        //原始文件
        File file = new File(path_);

        for (File f: file.listFiles()){
            //文件名
            String fileName = f.getName();
            //文件内容
            String fileContent = FileUtils.readFileToString(f,"utf8");
            System.out.println(fileContent);
            //文件路径
            String path = f.getPath();
            //文件大小
            long fileSize = FileUtils.sizeOf(f);

            //创建文件域名
            //域的名称 域的内容 是否存储
            Field fileNameField = new TextField("fileName", fileName, Field.Store.YES);
            Field fileContentField = new TextField("fileContent", fileContent, Field.Store.YES);
            Field filePathField = new TextField("filePath", path, Field.Store.YES);
            Field fileSizeField = new TextField("fileSize", fileSize+"", Field.Store.YES);

            //创建Document 对象
            Document indexableFields = new Document();
            indexableFields.add(fileNameField);
            indexableFields.add(fileContentField);
            indexableFields.add(filePathField);
            indexableFields.add(fileSizeField);
            //创建索引，并写入索引库
            indexWriter.addDocument(indexableFields);

        }

        //关闭indexWriter
        indexWriter.close();
    }
```
# 查询索引
``` javascript
@Test
    public void searchIndex() throws IOException {
    	String index_ = "src/main/resources/Lucene/index";
    	System.out.println(index_);
        //指定索引库存放路径
        //E:\Lucene_index
        Directory directory = FSDirectory.open(Paths.get(new File(index_).getPath()));
        //创建indexReader对象
        IndexReader indexReader = DirectoryReader.open(directory);
        //创建indexSearcher对象
        IndexSearcher indexSearcher = new IndexSearcher(indexReader);
        //创建查询
        Query query = new TermQuery(new Term("fileContent", "中文"));
        //执行查询
        //参数一  查询对象    参数二  查询结果返回的最大值
        TopDocs topDocs = indexSearcher.search(query, 10);
        System.out.println("查询结果的总数"+topDocs.totalHits);
        //遍历查询结果
        for (ScoreDoc scoreDoc: topDocs.scoreDocs){
            //scoreDoc.doc 属性就是doucumnet对象的id
            Document doc = indexSearcher.doc(scoreDoc.doc);
            System.out.println(doc.getField("fileName"));
//            System.out.println(doc.getField("fileContent"));
            System.out.println(doc.getField("filePath"));
            System.out.println(doc.getField("fileSize"));
        }
        indexReader.close();
    }
```

# 查询描述规则语法（查询解析语法）

``` sqf
3.1 Term 词项：

　　单个词项的表示：     电脑

　　短语的表示： "联想笔记本电脑"

3.2 Field 字段：

字段名: 

　　示例： name:“联想笔记本电脑” AND type:电脑

　　如果name是默认字段，则可写成： “联想笔记本电脑” AND type:电脑

　　如果查询串是：type:电脑 计算机 手机，只有第一个是type的值，后两个则是使用默认字段，翻译为type:动脑 OR name：计算机 OR name：手机

3.3 Term Modifiers 词项修饰符：

通配符：

? 单个字符

* 0个或多个字符

示例：te?t test* te*t

注意：通配符不可用在开头。

模糊查询，词后加 ~

示例： roam~

模糊查询最大支持两个不同字符。

示例： roam~1

正则表达式： /xxxx/

示例： /[mb]oat/

临近查询，短语后加 ~移动值

示例： "jakarta apache"~10

 范围查询：

mod_date:[20020101 TO 20030101]       包含边界值

title:{Aida TO Carmen} 不包含边界值

词项加权:

使该词项的相关性更高，通过 ^数值来指定加权因子，默认加权因子值是1

示例：如要搜索包含 jakarta apache 的文章，jakarta更相关，则：

jakarta^4 apache

短语也可以： "jakarta apache"^4 "Apache Lucene"

3.4  Boolean 操作符

Lucene支持的布尔操作： AND, “+”, OR, NOT ,"-"

AND：

"jakarta apache" AND "Apache Lucene"

+ 必须包含：

+jakarta lucene

OR：

"jakarta apache" jakarta = "jakarta apache" OR jakarta

NOT 非：

"jakarta apache" NOT "Apache Lucene“

注意：NOT不可单项使用： NOT “Apache Lucene“ 是不对的

- 同NOT：

"jakarta apache" -"Apache Lucene“

3.5 组合 ()

字句组合：

(jakarta OR apache) AND website

字段组合：

title:(+return +"pink panther")

3.6  转义 \

对语法字符： + - && || ! ( ) { } [ ] ^ “ ~ * ? : \ /     进行转义。

如要查询包含 (1+1):2需要转义为\(1\+1\)\:2
```

