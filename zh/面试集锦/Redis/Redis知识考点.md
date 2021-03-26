# 缓存知识考点

## 缓存中间件——Memcache和Redis的区别

- Memcache :代码层次类似Hash

- 支持简单数据类型 

- 不支持数据持久化存储 

- 不支持主从

-  不支持分片

  

- Redis数据类型丰富

- 支持数据磁盘持久化存储

- 支持主从

- 支持分片

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_09-57-35.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_10-02-02.png)

## Redis数据类型

**String** :字符串类型

**Hash** : 散列类型，相当于Java中的map

**List** : 列表类型(list)：有序、可重复, Redis的list是采用来链表来存储的，所以对于redis的list数据类型的操作，是操作list的两端数据来操作的。

**Set** : 集合类型    集合类型：无序、不可重复

**Sortedset** : Sortedset又叫zset,Sortedset是有序集合，可排序的，但是唯一。Sortedset和set的不同之处，是会给set中的元素添加一个分数，然后通过这个分数进行排序。



## 从海量key里查询出某一固定前缀的key

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_14-23-31.png)

> keys pattern 命令在redis服务器数据量过大时会造成客户端卡死

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_14-31-00.png)

## 如何通过redis实现分布式锁

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_14-54-25.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_14-57-16.png)

## 大量的key同时过期的注意项

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_14-56-31.png)

## 如何使用redis做异步队列

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-04-45.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-06-13.png)

**实例**

以下实例演示了发布订阅是如何工作的。在我们实例中我们创建了订阅频道名为 **redisChat**:

```shell
redis 127.0.0.1:6379> SUBSCRIBE redisChat

Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
```

现在，我们先重新开启个 redis 客户端，然后在同一个频道 redisChat 发布两次消息，订阅者就能接收到消息。

```shell
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"

(integer) 1

redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by runoob.com"

(integer) 1

# 订阅者的客户端会显示如下消息
1) "message"
2) "redisChat"
3) "Redis is a great caching technique"
1) "message"
2) "redisChat"
3) "Learn redis by runoob.com"
```


> https://www.runoob.com/redis/redis-pub-sub.html  菜鸟教程 redis发布订阅

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-19-01.png)

## Redis如何做持久化

### 1、RDB(快照)持久化

RDB(快照)持久化 : 保存某个时间点的全量数据快照

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-30-57.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-38-50.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-43-15.png)

### 2、AOF持久化

- AOF (Append-Only-File )持久化:保存写状态
- 记录下除了查询以外的所有变更数据库状态的指令
- 以append的形式追加保存到AOF文件中(增量)



![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-54-05.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-55-43.png)

## RDB和AOF的优缺点

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_15-57-14.png)

## Redis4.0默认方式

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-00-57.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-01-46.png)

## Redis Pipeline机制

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-05-51.png)

 

## Redis同步机制

### 主从同步机制

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-10-33.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-12-15.png)

### 主从同步+哨兵

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-15-24.png)

![](F:\BigDataNote\Bigdata\zh\面试集锦\Redis\assert\2019-08-23_16-42-07.png)

## Redis的集群原理

如何从海量数据里快速找到所需？

分片 ：按照某种规则去划分数据，分散存储到多个节点上

