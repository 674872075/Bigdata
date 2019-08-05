# 实时搜索引擎Elasticsearch

Elasticsearch（简称ES）是一个基于Apache Lucene(TM)的开源搜索引擎，无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。

### Elasticsearch简介

#### Elasticsearch是什么

Elasticsearch是一个基于Apache Lucene(TM)的开源搜索引擎，无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。 
但是，Lucene只是一个库。想要发挥其强大的作用，你需使用Java并要将其集成到你的应用中。Lucene非常复杂，你需要深入的了解检索相关知识来理解它是如何工作的。 
Elasticsearch也是使用Java编写并使用Lucene来建立索引并实现搜索功能，但是它的目的是通过简单连贯的RESTful API让全文搜索变得简单并隐藏Lucene的复杂性。 
不过，Elasticsearch不仅仅是Lucene和全文搜索引擎，它还提供：

- 分布式的实时文件存储，每个字段都被索引并可被搜索
- 实时分析的分布式搜索引擎
- 可以扩展到上百台服务器，处理PB级结构化或非结构化数据

而且，所有的这些功能被集成到一台服务器，你的应用可以通过简单的RESTful API、各种语言的客户端甚至命令行与之交互。上手Elasticsearch非常简单，它提供了许多合理的缺省值，并对初学者隐藏了复杂的搜索引擎理论。它开箱即用（安装即可使用），只需很少的学习既可在生产环境中使用。Elasticsearch在Apache 2 license下许可使用，可以免费下载、使用和修改。 
随着知识的积累，你可以根据不同的问题领域定制Elasticsearch的高级特性，这一切都是可配置的，并且配置非常灵活。

以上内容来自 [[百度百科\]](http://baike.baidu.com/link?url=8rIWfQSAzwZGn1HTjaaH4VLwqXzsplGUAqnWC9ux_8ktsh_wL3hMRN7uQX4Hu1C0ugVEUnyRTZv62dKMrxKnWq)

#### Elasticsearch中涉及到的重要概念

Elasticsearch有几个核心概念。从一开始理解这些概念会对整个学习过程有莫大的帮助。

（1） 接近实时（NRT） 
Elasticsearch是一个接近实时的搜索平台。这意味着，从索引一个文档直到这个文档能够被搜索到有一个轻微的延迟（通常是1秒）。

（2） 集群（cluster） 
一个集群就是由一个或多个节点组织在一起，它们共同持有你整个的数据，并一起提供索引和搜索功能。一个集群由一个唯一的名字标识，这个名字默认就是“elasticsearch”。这个名字是重要的，因为一个节点只能通过指定某个集群的名字，来加入这个集群。在产品环境中显式地设定这个名字是一个好习惯，但是使用默认值来进行测试/开发也是不错的。

（3） 节点（node） 
一个节点是你集群中的一个服务器，作为集群的一部分，它存储你的数据，参与集群的索引和搜索功能。和集群类似，一个节点也是由一个名字来标识的，默认情况下，这个名字是一个随机的漫威漫画角色的名字，这个名字会在启动的时候赋予节点。这个名字对于管理工作来说挺重要的，因为在这个管理过程中，你会去确定网络中的哪些服务器对应于Elasticsearch集群中的哪些节点。

一个节点可以通过配置集群名称的方式来加入一个指定的集群。默认情况下，每个节点都会被安排加入到一个叫做“elasticsearch”的集群中，这意味着，如果你在你的网络中启动了若干个节点，并假定它们能够相互发现彼此，它们将会自动地形成并加入到一个叫做“elasticsearch”的集群中。

在一个集群里，只要你想，可以拥有任意多个节点。而且，如果当前你的网络中没有运行任何Elasticsearch节点，这时启动一个节点，会默认创建并加入一个叫做“elasticsearch”的集群。

（4） 索引（index） 
一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母的），并且当我们要对对应于这个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。索引类似于关系型数据库中Database的概念。在一个集群中，如果你想，可以定义任意多的索引。

（5） 类型（type） 
在一个索引中，你可以定义一种或多种类型。一个类型是你的索引的一个逻辑上的分类/分区，其语义完全由你来定。通常，会为具有一组共同字段的文档定义一个类型。比如说，我们假设你运营一个博客平台并且将你所有的数据存储到一个索引中。在这个索引中，你可以为用户数据定义一个类型，为博客数据定义另一个类型，当然，也可以为评论数据定义另一个类型。类型类似于关系型数据库中Table的概念。

（6）文档（document） 
一个文档是一个可被索引的基础信息单元。比如，你可以拥有某一个客户的文档，某一个产品的一个文档，当然，也可以拥有某个订单的一个文档。文档以JSON（Javascript Object Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式。 
在一个index/type里面，只要你想，你可以存储任意多的文档。注意，尽管一个文档，物理上存在于一个索引之中，文档必须被索引/赋予一个索引的type。文档类似于关系型数据库中Record的概念。实际上一个文档除了用户定义的数据外，还包括`_index`、`_type`和`_id`字段。

（7） 分片和复制（shards & replicas） 
一个索引可以存储超出单个结点硬件限制的大量数据。比如，一个具有10亿文档的索引占据1TB的磁盘空间，而任一节点都没有这样大的磁盘空间；或者单个节点处理搜索请求，响应太慢。

为了解决这个问题，Elasticsearch提供了将索引划分成多份的能力，这些份就叫做分片。当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。 
分片之所以重要，主要有两方面的原因：

- 允许你水平分割/扩展你的内容容量
- 允许你在分片（潜在地，位于多个节点上）之上进行分布式的、并行的操作，进而提高性能/吞吐量

至于一个分片怎样分布，它的文档怎样聚合回搜索请求，是完全由Elasticsearch管理的，对于作为用户的你来说，这些都是透明的。

在一个网络/云的环境里，失败随时都可能发生，在某个分片/节点不知怎么的就处于离线状态，或者由于任何原因消失了。这种情况下，有一个故障转移机制是非常有用并且是强烈推荐的。为此目的，Elasticsearch允许你创建分片的一份或多份拷贝，这些拷贝叫做复制分片，或者直接叫复制。复制之所以重要，主要有两方面的原因：

- 在分片/节点失败的情况下，提供了高可用性。因为这个原因，注意到复制分片从不与原/主要（original/primary）分片置于同一节点上是非常重要的。
- 扩展你的搜索量/吞吐量，因为搜索可以在所有的复制上并行运行

总之，每个索引可以被分成多个分片。一个索引也可以被复制0次（意思是没有复制）或多次。一旦复制了，每个索引就有了主分片（作为复制源的原来的分片）和复制分片（主分片的拷贝）之别。分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制数量，但是不能改变分片的数量。

默认情况下，Elasticsearch中的每个索引被分片5个主分片和1个复制，这意味着，如果你的集群中至少有两个节点，你的索引将会有5个主分片和另外5个复制分片（1个完全拷贝），这样的话每个索引总共就有10个分片。一个索引的多个分片可以存放在集群中的一台主机上，也可以存放在多台主机上，这取决于你的集群机器数量。主分片和复制分片的具体位置是由ES内在的策略所决定的。

#### 添加索引库(配置mapping映射)

```json
put请求   http://127.0.0.1:9200/blog

{
	"mappings": {
		"article": {
			"properties": {
				"id": {
					"type": "long",
					"store": true
				},
				"title": {
					"type": "text",
					"store": true,
					"index": true,
					"analyzer": "standard"
				},
				"content": {
					"type": "text",
					"store": true,
					"index": true,
					"analyzer": "standard"
				}
			}
		}
	}
}
```

#### 集群配置文件配置(配置一)

```properties
#节点1的配置信息:#集群名称,保证唯一
cluster.name: my-elasticsearch
#节点名称,必须不一样
node.name: node-1
#必须为本机的ip地址
network.host: 127.0.0.1 # 0.0.0.0 允许绑定任意ip，且允许外网访问
#服务端口号,在同一机器下必须不一样
http.port: 9201
#集群间通信端口号,在同一机器下必须不一样
transport.tcp.port: 9301
#设置集群自动发现机器ip集合
discovery.zen.ping.unicast.hosts: ["127.0.0.1:9301", "127.0.0.1:9302", "127.0.0.1:9303"]
```

**配置二**

```properties
cluster.name: xuecheng
node.name: xc_node_1
network.host: 0.0.0.0
http.port: 9200
transport.tcp.port: 9300
node.master: true
node.data: true
discovery.zen.ping.unicast.hosts: ["0.0.0.0:9300", "0.0.0.0:9301"]
discovery.zen.minimum_master_nodes: 1
node.ingest: true
bootstrap.memory_lock: false
node.max_local_storage_nodes: 2

path.data: D:\ElasticSearch\elasticsearch-6.2.1-1\data
path.logs: D:\ElasticSearch\elasticsearch-6.2.1-1\logs

http.cors.enabled: true
http.cors.allow-origin: /.*/
```



#### 使用Java客户端创建索引库

```xml
 <dependencies>
        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>5.6.8</version>
        </dependency>

        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>transport</artifactId>
            <version>5.6.8</version>
        </dependency>

        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-to-slf4j</artifactId>
            <version>2.9.1</version>
        </dependency>


        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.24</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.21</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

```java
@Test
    public  void createIndex() throws UnknownHostException {
        //1、创建一个Setting对象,相当于是一个配置信息,主要配置集群的名称
        Settings build = Settings.builder().put("cluster.name","my-elasticsearch")
                .build();
        //2.创建一个客户端Client对象
        TransportClient client = new PreBuiltTransportClient(build);
        //设置访问的elasticsearch节点，为保证节点的高可用，三台节点都进行设置
        //InetSocketTransportAddress 参数1:域名 参数2:TCP端口
        //InetAddress.getByName("127.0.0.1") 将ip转化为域名
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"),9301));
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"),9302));
        client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"),9303));
        //3.使用client对象创建一个索引库
        client.admin().indices().prepareCreate("index_hello")
                 //执行操作
                .get();
        //4.关闭client对象
        client.close();
    }
```

#### 2、使用Java客户端设置Mappings步骤

​	1)创建一个Settings对象

​	2)创建一个client对象

​	3)创建一个mapping信息,应该是一个json数据,可以是字符串,也可以是xContextBuilder对象 

​	4)使用client向es服务器发送mapping信息

​	5)关闭client对象

#### 3、添加文档步骤

​	1)创建一个Settings对象

​	2)创建一个c1ient对象

​	3)创建一个文档对象,创建一个json格式的字符串,或者使用XContentBuilder

​	4)使用Client对象吧文档添加到索引库中

​	5)关闭client

### Spring Data ElasticSearch入门

#### 导入依赖

```xml
 <dependencies>
        <dependency>
            <groupId>org.elasticsearch</groupId>
            <artifactId>elasticsearch</artifactId>
            <version>5.6.8</version>
        </dependency>
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>transport</artifactId>
            <version>5.6.8</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-to-slf4j</artifactId>
            <version>2.9.1</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.24</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.21</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>


        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.8.1</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.1</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.8.1</version>
        </dependency>


        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-elasticsearch</artifactId>
            <version>3.0.5.RELEASE</version>
            <exclusions>
                <exclusion>
                    <groupId>org.elasticsearch.plugin</groupId>
                    <artifactId>transport-netty4-client</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.0.4.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
    </dependencies>
```

#### 创建applicationContext.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:elasticsearch="http://www.springframework.org/schema/data/elasticsearch"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/data/elasticsearch
		http://www.springframework.org/schema/data/elasticsearch/spring-elasticsearch-1.0.xsd
		">

    <!--配置包扫描器 扫描Dao包，自动创建实例 -->
    <elasticsearch:repositories base-package="com.zh.dao"/>

    <!-- 扫描Service包，创建Service的实体 -->
    <context:component-scan base-package="com.zh.service"/>

    <!-- 配置elasticSearch的连接 -->
    <elasticsearch:transport-client id="client" cluster-nodes="127.0.0.1:9301,127.0.0.1:9302,127.0.0.1:9303" cluster-name="my-elasticsearch"/>


    <!-- elasticsearchTemplate模版对象 -->
    <bean id="elasticsearchTemplate" class="org.springframework.data.elasticsearch.core.ElasticsearchTemplate">
        <constructor-arg name="client" ref="client"></constructor-arg>
    </bean>

</beans>
```

#### 编写实体Article

```java
package com.zh.entity;

import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.Field;
import org.springframework.data.elasticsearch.annotations.FieldType;

@Document(indexName = "sdes_blog",type = "article")
@Data
public class Article {

    //文档id
    @Id
    @Field(type = FieldType.Long,store = true)
    private  Long id;
    @Field(type = FieldType.text,store = true,analyzer = "ik_smart")
    private String title;
    @Field(type = FieldType.text,store = true,analyzer = "ik_smart")
    private String content;
}

```

#### 编写dao

```java
package com.zh.dao;

import com.zh.entity.Article;
import org.springframework.data.elasticsearch.repository.ElasticsearchRepository;
//ElasticsearchRepository<T,ID>  T 操作的实体类类型  ID 实体类中主键属性的类型
public interface ArticleRepository extends ElasticsearchRepository<Article,Long> {
}

```

#### 注意事项

> 其中，注解解释如下：
> @Document(indexName="sdes_blog",type="article")：
>     indexName：索引的名称（必填项）
>     type：索引的类型
> @Id：主键的唯一标识
> @Field(index=true,analyzer="ik_smart",store=true,searchAnalyzer="ik_smart",type = FieldType.text)
>     index：是否设置分词
>     analyzer：存储时使用的分词器
>     searchAnalyze：搜索时使用的分词器
>     store：是否存储
>     type: 数据类型

#### CRUD操作

```java
package com.zh;

import com.zh.dao.ArticleRepository;
import com.zh.entity.Article;
import org.elasticsearch.index.query.QueryBuilders;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.elasticsearch.core.ElasticsearchTemplate;
import org.springframework.data.elasticsearch.core.query.NativeSearchQueryBuilder;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;
import java.util.Optional;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringDataElasticSearchTest {

    @Autowired
    private ArticleRepository articleRepository;

    @Autowired
    private ElasticsearchTemplate elasticsearchTemplate;

    /**
     * 创建索引库
     */
    @Test
    public void createIndex(){
        //创建索引库
        elasticsearchTemplate.createIndex(Article.class);
        //配置mapping
       // elasticsearchTemplate.putMapping(Article.class);
    }


    /**
     * 保存文档
     */
    @Test
    public void  save(){
            Article article = new Article();
            article.setId(20L);
            article.setTitle("很久没发微博了");
            article.setContent("Spring Data ElasticSearch 基于 spring data API 简化 elasticSearch操作，将原始操作elasticSearch的客户端API 进行封装 \n" +
                    "    Spring Data为Elasticsearch Elasticsearch项目提供集成搜索引擎");
            articleRepository.save(article);
    }

    /**
     * 更新文档(先删除后添加)
     */
    @Test
    public void update(){
        Article article = new Article();
        article.setId(100L);
        article.setTitle("测试SpringData ElasticSearch");
        article.setContent("很久之前就安装了elasticsearch,一直没用java用过,最近看了一下spring data系列的elasticsearch,这里写一篇心得");
        articleRepository.save(article);

    }

    /**
     * 根据id删除文档
     */
    @Test
    public void delete(){
        articleRepository.deleteById(2L);
    }

    /**
     * 根据文档id进行查询
     */
    @Test
    public void findById(){
        Optional<Article> articles = articleRepository.findById(10L);
        if(articles.isPresent()) {
            Article article = articles.get();
            System.out.println(article.toString());
        }
    }

    /**
     * 查询所有文档
     */
    @Test
    public void findByAll(){
        Iterable<Article> articles = articleRepository.findAll();
        articles.forEach(System.out::println);
    }

    /**
     * 自定义查询方法
     */
    @Test
    public void find(){
        /**
         * 一个词 或者一句话 一句话的话会分词但词语之间是and关系(must)
         */
        List<Article> articles = articleRepository.getArticleByTitle("微");
        articles.forEach(System.out::println);
    }

    /**
     * 原生查询
     */
    @Test
    public  void nativeFind(){
        /**
         * 一个词 或者一句话 一句话的话会分词但词语之间是or关系
         */
        NativeSearchQueryBuilder nativeSearchQueryBuilder = new NativeSearchQueryBuilder()
                .withQuery(QueryBuilders.queryStringQuery("微").defaultField("title"))
                .withPageable(PageRequest.of(0, 15));

        Page<Article> search = articleRepository.search(nativeSearchQueryBuilder.build());
        search.forEach(a-> System.out.println(a));
        /*List<Article> articles = elasticsearchTemplate.queryForList(nativeSearchQueryBuilder.build(), Article.class);
        articles.forEach(a-> System.out.println(a));*/
    }

}

```

#### Sring Data JPA自定义方法命名规则

下面的表格是Elasticsearch module根据方法名创建查询的关键字。

| 关键字              | 示例                                     | Elasticsearch json查询                                       |
| ------------------- | ---------------------------------------- | ------------------------------------------------------------ |
| And                 | findByNameAndPrice                       | {"bool" : {"must" : [ {"field" : {"name" : "?"}}, {"field" : {"price" : "?"}} ]}} |
| Or                  | findByNameOrPrice                        | {"bool" : {"should" : [ {"field" : {"name" : "?"}}, {"field" : {"price" : "?"}} ]}} |
| Is                  | findByName                               | {"bool" : {"must" : {"field" : {"name" : "?"}}}}             |
| Not                 | findByNameNot                            | {"bool" : {"must_not" : {"field" : {"name" : "?"}}}}         |
| Between             | findByPriceBetween                       | {"bool" : {"must" : {"range" : {"price" : {"from" : ?,"to" : ?,"include_lower" : true,"include_upper" : true}}}}} |
| LessThanEqual       | findByPriceLessThan                      | {"bool" : {"must" : {"range" : {"price" : {"from" : null,"to" : ?,"include_lower" : true,"include_upper" : true}}}}} |
| GreaterThanEqual    | findByPriceGreaterThan                   | {"bool" : {"must" : {"range" : {"price" : {"from" : ?,"to" : null,"include_lower" : true,"include_upper" : true}}}}} |
| Before              | findByPriceBefore                        | {"bool" : {"must" : {"range" : {"price" : {"from" : null,"to" : ?,"include_lower" : true,"include_upper" : true}}}}} |
| After               | findByPriceAfter                         | {"bool" : {"must" : {"range" : {"price" : {"from" : ?,"to" : null,"include_lower" : true,"include_upper" : true}}}}} |
| Like                | findByNameLike                           | {"bool" : {"must" : {"field" : {"name" : {"query" : "?*","analyze_wildcard" : true}}}}} |
| StartingWith        | findByNameStartingWith                   | {"bool" : {"must" : {"field" : {"name" : {"query" : "?*","analyze_wildcard" : true}}}}} |
| EndingWith          | findByNameEndingWith                     | {"bool" : {"must" : {"field" : {"name" : {"query" : "*?","analyze_wildcard" : true}}}}} |
| Contains/Containing | findByNameContaining                     | {"bool" : {"must" : {"field" : {"name" : {"query" : "?","analyze_wildcard" : true}}}}} |
| In                  | findByNameIn(Collection<String>names)    | {"bool" : {"must" : {"bool" : {"should" : [ {"field" : {"name" : "?"}}, {"field" : {"name" : "?"}} ]}}}} |
| NotIn               | findByNameNotIn(Collection<String>names) | {"bool" : {"must_not" : {"bool" : {"should" : {"field" : {"name" : "?"}}}}}} |
| Near                | findByStoreNear                          | 暂不支持                                                     |
| True                | findByAvailableTrue                      | {"bool" : {"must" : {"field" : {"available" : true}}}}       |
| False               | findByAvailableFalse                     | {"bool" : {"must" : {"field" : {"available" : false}}}}      |
| OrderBy             | findByAvailableTrueOrderByNameDesc       | {"sort" : [{ "name" : {"order" : "desc"} }],"bool" : {"must" : {"field" : {"available" : true}}}} |