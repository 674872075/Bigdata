[TOC]

#  一、什么是Lucene

> Lucene是一个基于Java开发全文检索工具包.即全文搜索引擎

# 二、Lucene实现全文检索的流程

## 1、创建索引

### 1)获得文档

​		原始文档:要基于哪些数据来进行搜索,那么这些数据就是原始文档.

​		搜索引擎:使用爬虫获得原始文档

​		站内搜索:数据库中的数据.

​		案例:直接使用io流取磁盘上的文件.

### 2)构建文档对象

​		对应每个原始文档创建一个Document对象

​		每document对象中包含多个域(field)

​		域中保存的都是原始文档数据

​				域的名称

​				域的值

​		每个文档都有一个唯一的编号,就是文档id.

**注意：每个Document可以有多个Field，不同的Document可以有不同的Field，同一个Document可以有相同的Field（域名和域值都相同）**

------

**每个文档都有一个唯一的编号，就是文档id。**

### 3)分析文档

​		就是分词的过程

​					1、根据空格进行字符串拆分,得到一个单词列表

​					**2、把单词统一转换成小写**

​					3、去除标点符号

​					4、去除停用词:无意义的词

​					每个关键词都封装成一个Term对象中.

​							Term中包含两部分内容

​									关键词所在的域

​									关键词本身

​							不同的域中拆分出来的相同的关键词是不同的Term.

**每个单词叫做一个Term，不同的域中拆分出来的相同的单词是不同的term。term中包含两部分一部分是文档的域名，另一部分是单词的内容。**

**例如：文件名中包含apache和文件内容中包含的apache是不同的term。**

### 4)创建索引

​	基于关键词列表创建一个索引。保存到索引库中.

​						索引库中:

​								索引

​								document对象

​								关键词和文档的对应关系

​								通过词语找文档,这种索引的结构叫倒排索引结构

## 2、查询索引

### 1)用户查询接口

​		用户输入查询条件的地方

​		例如:百度的搜索框

### 2)把关键词封装成一个查询对象

​		要查询的域搜索的关键词

### 3)执行查询

​		根据要查询的关键词到对应的域上进行搜索.找到关键词,根据关键词找到对应的文档 

### 4)渲染结果

​		根据文档的id找到文档对象

​		对关键词进行高亮显示

​		分页处理

​		最终展示给用户看.



## 3.入门程序

### 1)创建java工程 添加jar包
```jar
commons-io-2.6.jar  #apache的io工具包
lucene-analyzers-common-7.4.0.jar #lucene的分析包
lucene-core-7.4.0.jar   #lucene的核心包
```

### 2)创建索引和查询索引(可以使用luke工具查看索引)
```java
package com.zh.lucene;
import org.apache.commons.io.FileUtils;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.document.TextField;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.index.IndexWriterConfig;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.FSDirectory;
import org.junit.Test;

import java.io.File;
import java.io.IOException;

public class LuceneFirst {

    @Test
      /**
     * 创建索引
     */
    public  void createIndex() throws IOException {
        //第一步：创建一个java工程，并导入jar包。
        //第二步：创建一个indexwriter对象。
        //1）指定索引库的存放位置Directory对象
        //把索引存放在内存中
        //RAMDirectory ramDirectory = new RAMDirectory();
        //把索引存放到本地磁盘
        Directory directory = FSDirectory.open(new File("D:/luceneTemp/index").toPath());
        //基于Directory对象创建一个创建一个Indexwriter对象
        // 2）指定一个IndexWriterConfig对象  使用标准分析器
        //IndexWriter indexWriter = new IndexWriter(directory, new IndexWriterConfig());
        //使用中文分析器
        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new IKAnalyzer());
        IndexWriter indexWriter = new IndexWriter(directory,indexWriterConfig);
        //读取磁盘上的文件
        File dir = new File("E:\\微服务视频\\资料\\61.Lucene\\lucene\\02.参考资料\\searchsource");
        File[] files = dir.listFiles();
        for (File file : files) {
            //获取文件名
            String fileName = file.getName();
            //文件路径
            String filePath = file.getPath();
            //文件内容
            String fileContent = FileUtils.readFileToString(file, "utf-8");
            //文件大小
            long fileSize = FileUtils.sizeOf(file);
            //创建Field
            //参数1:域的名称，参数2:域的内容,参数3:是否存储
            //分词 索引 存储（可选）
            Field fieldName = new TextField("name", fileName, Field.Store.YES);
           // Field fieldPath = new TextField("path", filePath, Field.Store.YES);
            //不分词 不索引 只存储
            Field fieldPath = new StoredField("path", filePath);
            Field fieldContent = new TextField("content", fileContent, Field.Store.YES);
            //Field fieldSize = new TextField("size", fileSize + "", Field.Store.YES);
            //分词 索引 不存储
            Field fieldSizeValue = new LongPoint("size", fileSize);
            Field fieldSizeStore = new StoredField("size", fileSize);
            //第二步：创建document对象。
            Document document = new Document();
            //第三步：创建field对象，将field添加到document对象中。
            document.add(fieldName);
            document.add(fieldPath);
            document.add(fieldContent);
            document.add(fieldSize);
            //第四步：使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引库。
            indexWriter.addDocument(document);
        }
        //第五步：关闭IndexWriter对象。
        indexWriter.close();
    }
    
     @Test
    /**
     * 查询索引 #查询关键词spring 需要指定域 打包成Term
     */
    public void searchIndex() throws Exception{
        //1.创建一个Director对象，指定索引库的位置
        Directory directory = FSDirectory.open(new File("D:/luceneTemp/index").toPath());
        //2.创建一个IndexReader对象
        IndexReader indexReader = DirectoryReader.open(directory);
        //3.创建一个IndexSearch对象，构造方法中的参数IndexReader对象
        IndexSearcher indexSearcher = new IndexSearcher(indexReader);
        //4.创建一个Query对象，TermQuery
        TermQuery termQuery = new TermQuery(new Term("content","spring"));
        //5.执行查询,得到TopDocs对象
        //参数1：查询对象 参数2：查询结果返回的最大记录数
        TopDocs topDocs = indexSearcher.search(termQuery, 10);
        //6.获取查询结果的总记录数
        System.out.println("查询的总记录数:"+topDocs.totalHits);
        //7.获取文档列表
        ScoreDoc[] scoreDocs =  topDocs.scoreDocs;
        //8.打印文档中的内容
        for (ScoreDoc scoreDoc : scoreDocs) {
            //获取文档id
            int id = scoreDoc.doc;
            //根据id得到document对象
            Document doc = indexSearcher.doc(id);
            System.out.println(doc.get("name"));
            System.out.println(doc.get("path"));
           // System.out.println(doc.get("content"));
            System.out.println(doc.get("size"));
            System.out.println("----------------");
        }
        //9.关闭IndexReader对象
        indexReader.close();
    }
}

```

> 可以使用luke查看创建后的索引

### 3)查看分析器的分析效果

```java
@Test
    /**
     * 查看标准分析器分析效果
     */
    public void testTokenStream() throws Exception {
        //1.创建一个Analyzer对象,StandardAnalyzer对象 标准分析器
        Analyzer analyzer = new StandardAnalyzer();
        //2.使用分析器对象的tokenStream方法获得一个TokenStream对象
        //参数1:域名称  参数2：分析的文本内容
        TokenStream tokenStream = analyzer.tokenStream("", "Learn how to create a web page with Spring MVC.");
        //3.向TokenStream对象中设置一个引用，相当于数一个指针
        CharTermAttribute charTermAttribute = tokenStream.addAttribute(CharTermAttribute.class);
        //4.调用 TokenStream对象的rest方法，如果不调用会抛出异常
        tokenStream.reset();
        //5.使用while循环遍历TokenStream对象
        while (tokenStream.incrementToken()) {
            System.out.println(charTermAttribute.toString());
        }
        //关闭TokenStream对象
        tokenStream.close();
    }
```

### 4)IKAnalyze的使用方法(中文分析器)

​	1.把IKAnalyze的jar包添加到工程中

> IK-Analyzer-1.0-SNAPSHOT.jar

​	2.把配置文件和扩展词典添加到classpath下

​					hotword.dic
​					IKAnalyzer.cfg.xml
​					stopword.dic

​	注意：扩展词典严禁使用windows记事本编辑(utf-8+BOM)保证扩展词典的编码是utf-8(无BOM)

 	扩展词典：添加一些新词

​	停用词词典：无意义的词或者敏感词汇

```java
//Analyzer analyzer = new StandardAnalyzer();
Analyzer analyzer = new IKAnalyzer(); //中文分析器
```



# 三.索引库维护

##  1、Field域的属性

**是否分析**：是否对域的内容进行分词处理。前提是我们要对域的内容进行查询。

**是否索引**：将Field分析后的词或整个Field值进行索引，只有索引方可搜索到。

比如：商品名称、商品简介分析后进行索引，订单号、身份证号不用分析但也要索引，这些将来都要作为查询条件。

**是否存储**：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取

比如：商品名称、订单号，凡是将来要从Document中获取的Field都要存储。

 

**是否存储的标准：是否要将内容展示给用户**

 

| Field类                                                      | 数据类型               | Analyzed   是否分析 | Indexed   是否索引 | Stored   是否存储 | 说明                                                         |
| ------------------------------------------------------------ | ---------------------- | ------------------- | ------------------ | ----------------- | ------------------------------------------------------------ |
| StringField(FieldName,   FieldValue,Store.YES))              | 字符串                 | N                   | Y                  | Y或N              | 这个Field用来构建一个字符串Field，但是不会进行分析，会将整个串存储在索引中，比如(订单号,姓名等)   是否存储在文档中用Store.YES或Store.NO决定 |
| LongPoint(String name, long... point)                        | Long型                 | Y                   | Y                  | N                 | 可以使用LongPoint、IntPoint等类型存储数值类型的数据。让数值类型可以进行索引。但是不能存储数据，如果想存储数据还需要使用StoredField。 |
| StoredField(FieldName, FieldValue)                           | 重载方法，支持多种类型 | N                   | N                  | Y                 | 这个Field用来构建不同类型Field   不分析，不索引，但要Field存储在文档中 |
| TextField(FieldName, FieldValue, Store.NO)   或   TextField(FieldName, reader) | 字符串   或   流       | Y                   | Y                  | Y或N              | 如果是一个Reader, lucene猜测内容比较多,会采用Unstored的策略. |

>  优化后的代码

```java
 @Test
    /**
     * 创建索引
     */
    public void createIndex() throws IOException {
        //第一步：创建一个java工程，并导入jar包。
        //第二步：创建一个indexwriter对象。
        //1）指定索引库的存放位置Directory对象
        //把索引存放在内存中
        //RAMDirectory ramDirectory = new RAMDirectory();
        //把索引存放到本地磁盘
        Directory directory = FSDirectory.open(new File("D:/luceneTemp/index").toPath());
        //基于Directory对象创建一个创建一个Indexwriter对象
        // 2）指定一个IndexWriterConfig对象
        //IndexWriter indexWriter = new IndexWriter(directory, new IndexWriterConfig());
        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new IKAnalyzer());
        IndexWriter indexWriter = new IndexWriter(directory,indexWriterConfig);
        //读取磁盘上的文件
        File dir = new File("E:\\微服务视频\\资料\\61.Lucene\\lucene\\02.参考资料\\searchsource");
        File[] files = dir.listFiles();
        for (File file : files) {
            //获取文件名
            String fileName = file.getName();
            //文件路径
            String filePath = file.getPath();
            //文件内容
            String fileContent = FileUtils.readFileToString(file, "utf-8");
            //文件大小
            long fileSize = FileUtils.sizeOf(file);
            //创建Field
            //参数1:域的名称，参数2:域的内容,参数3:是否存储
            //分词 索引 存储（可选）
            Field fieldName = new TextField("name", fileName, Field.Store.YES);
           // Field fieldPath = new TextField("path", filePath, Field.Store.YES);
            //不分词 不索引 只存储
            Field fieldPath = new StoredField("path", filePath);
            Field fieldContent = new TextField("content", fileContent, Field.Store.YES);
            //Field fieldSize = new TextField("size", fileSize + "", Field.Store.YES);
            //分词 索引 不存储
            Field fieldSizeValue = new LongPoint("size", fileSize);
            Field fieldSizeStore = new StoredField("size", fileSize);

            //第二步：创建document对象。
            Document document = new Document();
            //第三步：创建field对象，将field添加到document对象中。
            document.add(fieldName);
            document.add(fieldPath);
            document.add(fieldContent);
            document.add(fieldSizeValue);
            document.add(fieldSizeStore);
            //第四步：使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引库。
            indexWriter.addDocument(document);
        }
        //第五步：关闭IndexWriter对象。
        indexWriter.close();
    }
```



## 2、添加文档

```java
  //添加索引
@Test
public void addDocument() throws Exception {
    //索引库存放路径
    Directory directory = FSDirectory.open(new File("D:\\temp\\index").toPath());
    IndexWriterConfig config = new IndexWriterConfig(new IKAnalyzer());
    //创建一个indexwriter对象
    IndexWriter indexWriter = new IndexWriter(directory, config);
    //创建一个Document对象
    Document document = new Document();
    //向document对象中添加域。
    //不同的document可以有不同的域，同一个document可以有相同的域。
    document.add(new TextField("filename", "新添加的文档", Field.Store.YES));
    document.add(new TextField("content", "新添加的文档的内容", Field.Store.NO));
    //LongPoint创建索引
    document.add(new LongPoint("size", 1000l));
    //StoreField存储数据
    document.add(new StoredField("size", 1000l));
    //不需要创建索引的就使用StoreField存储
    document.add(new StoredField("path", "d:/temp/1.txt"));
    //添加文档到索引库
    indexWriter.addDocument(document);
    //关闭indexwriter
    indexWriter.close();

}

```



## 3、删除文档

### 1)删除全部	

```java
 /**
     * 删除全部索引
     * @throws IOException
     */
    @Test
    public  void deleteIndexAll() throws IOException {
        IndexWriter indexWriter = getIndexWriter();
        //删除全部索引
        indexWriter.deleteAll();
        indexWriter.close();
    }

    /**
     *
     获取IndexWriter对象
     */
    private  IndexWriter getIndexWriter() throws IOException {
        Directory directory = FSDirectory.open(new File("D:/luceneTemp/index").toPath());
        IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new IKAnalyzer());
        IndexWriter indexWriter = new IndexWriter(directory,indexWriterConfig);
        return indexWriter;
    }
```

​	
### 2)根据查询、关键词删除文档,

```java
//根据查询条件删除索引
	@Test
	public void deleteIndexByQuery() throws Exception {
		IndexWriter indexWriter = getIndexWriter();
		//创建一个查询条件
		Query query = new TermQuery(new Term("name", "apache"));
		//根据查询条件删除
		indexWriter.deleteDocuments(query);
		//关闭indexwriter
		indexWriter.close();
	}

```



## 4、 修改文档

> 修改的原理是先删除后添加

```java
//修改索引库
@Test
public void updateIndex() throws Exception {
    IndexWriter indexWriter = getIndexWriter();
    //创建一个Document对象
    Document document = new Document();
    //向document对象中添加域。
    //不同的document可以有不同的域，同一个document可以有相同的域。
    document.add(new TextField("filename", "要更新的文档", Field.Store.YES));
    document.add(new TextField("content", " Lucene 简介 Lucene 是一个基于 Java 的全文信息检索工具包," +
                                                       "它不是一个完整的搜索应用程序,而是为你的应用程序提供索引和搜索功能。",
                Field.Store.YES));
    indexWriter.updateDocument(new Term("content", "java"), document);
    //关闭indexWriter
    indexWriter.close();
}

```



# 四、索引库查询

 ## 1、使用Query的子类

### 1) TermQuery根据关键词进行查询

​	需要指定要查询的域及其查询的关键词

```java
//使用Termquery查询
@Test
public void testTermQuery() throws Exception {
    Directory directory = FSDirectory.open(new File("D:\\temp\\index").toPath());
    IndexReader indexReader = DirectoryReader.open(directory);
    IndexSearcher indexSearcher = new IndexSearcher(indexReader);
    
    //创建查询对象
    Query query = new TermQuery(new Term("content", "lucene"));
    //执行查询
    TopDocs topDocs = indexSearcher.search(query, 10);
    //共查询到的document个数
    System.out.println("查询结果总数量：" + topDocs.totalHits);
    /*
    *获取文档id   int id = scoreDoc.doc;
    */
    //遍历查询结果
    for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
        Document document = indexSearcher.doc(scoreDoc.doc);
        System.out.println(document.get("name"));
        System.out.println(document.get("content"));
        System.out.println(document.get("path"));
        System.out.println(document.get("size"));
    }
    //关闭indexreader
    indexSearcher.getIndexReader().close();
}

```

### 2) RangeQuery范围查询

```java
@Test
public void testRangeQuery() throws Exception {
    IndexSearcher indexSearcher = getIndexSearcher();
    Query query = LongPoint.newRangeQuery("size", 0L, 10000L);
    printResult(query, indexSearcher);
}
```



## 2、使用QueryPaser进行查询

​	可以对要查询的内容先分词,然后基于分词的结果进行查询.

​	添加一个jar包

> ​	lucene-queryparser-7.4.e.jar

```java
@Test
public void testQueryParser() throws Exception {
    IndexSearcher indexSearcher = getIndexSearcher();
    //创建queryparser对象
    //第一个参数默认搜索的域
    //第二个参数就是分析器对象
    QueryParser queryParser = new QueryParser("content", new IKAnalyzer());
    Query query = queryParser.parse("Lucene是java开发的");
    //执行查询
    printResult(query, indexSearcher);
}

private void printResult(Query query, IndexSearcher indexSearcher) throws Exception {
    //执行查询
    TopDocs topDocs = indexSearcher.search(query, 10);
    //共查询到的document个数
    System.out.println("查询结果总数量：" + topDocs.totalHits);
    //遍历查询结果
    for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
        Document document = indexSearcher.doc(scoreDoc.doc);
        System.out.println(document.get("filename"));
        //System.out.println(document.get("content"));
        System.out.println(document.get("path"));
        System.out.println(document.get("size"));
    }
    //关闭indexreader
    indexSearcher.getIndexReader().close();
}
```

 