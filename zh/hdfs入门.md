# hadoop的起源 #：

**起源于一个开源的项目nutch，主要是做通用爬虫的，遇到两个问题，一个是数据量太大，没法搜索，一个是数据量太大没法存储**

	检索的问题：lucene

	数据的存储问题：看到谷歌开源的三篇论文 GFS(google  fileSystem)，MapReduce(分布式计算框架)  BigTable(key,value对的非关系型数据库)
	产生两个框架  hadoop(HDFS+MapReduce)   HBase
	
	
	hadoop：从广义上来说是指代一个大数据软件的生态圈，包括各种周边的其他框架
	
	
	hadoop的发行版本：主要从0.x到1.x到2.x这三个主流的版本


# 三大主流公司： #
	apache开源版本：commiter  优点：版本更新迭代比较快，所有的软件都有对应的迭代
		        缺点：版本的升级，兼容，维护等都比较麻烦
			实际生产环境当中，尽量不要使用apache的版本
	
	免费开源版本hortonworks：软件的安装，升级等都做了
	
	服务收费版本cloudera：软件有收费版，也有免费版
	
	MapR:大数据软件厂商



# hadoop 1.x与2.x的架构模型介绍: #

**1.x架构模型：主要分为两大块，一块是分布式文件存储，一块是分布式文件计算**

	      分布式文件存储：HDFS
			nameNode：主节点，主要是用来维护元数据信息
			dataNode：从节点，主要用于存储数据
			SecondaryNameNode:作用就是用于合并元数据信息，辅助namenode管理元数据信息

		分布式文件计算系统：MapReduce
			JobTracker：主节点，主要功能有接收用户提交的计算任务，分配任务给从节点执行
			taskTracker：从节点，主要用于接收主节点分配的任务，并执行任务


**元数据信息：描述数据的数据**

	第一个问题：如果有一堆书，如何快速查找到我需要的时间简史  书本的分类，书本的编号，书本所在的书架，书本的位置，如果记录了这些信息，就可以快速找到对应的书本
	这些信息，描述了我们需要的书本在哪里，确定了这些描述信息，就可以唯一定位到这本书
	
	如何区分你的同桌：性别，外观样貌信息（环肥燕瘦） 长头发，短头发，都是通过一些描述信息，来区分每一个人的
	
	如何设计一个文件系统：
	第一个：盘符   
	第二个：文件名 
	第三个：文件的类型  
	第四个：文件大小   
	第五个：创建时间修改时间
	第六个：所属权限  
	第七个：文件的路径
	这些数据都是用于描述一个文件或者文件夹，只要有了这些描述信息，那么我们就能定位到一个唯一的文件或者文件夹
	这些都是一些描述数据，叫做我们的元数据信息
	
	元数据信息：文件名称，文件路径，文件的大小，文件的权限

每一个文件或者每一个文件夹都会产生一份元数据信息
只要抓住了元数据信息，就抓住了磁盘上面存储的所有的文件或者文件夹

1.x当中，主节点  namenode  jobtracker都存在单节点故障问题


**hadoop2.x的架构：
文件存储系统 HDFS：**

	namenode：主节点。主要用于维护元数据信息
	secondaryNamenode：辅助namenode管理元数据信息
	DataNode：存储各种各样的数据

**资源调度系统：yarn平台**

	resourceManager：主要两个作用，接收用户的计算任务请求，分配资源
	nodeManager：主要用于接收appmaster分配的任务
	AppMaster：resourceManager为每个计算任务启动一个AppMaster，AppMaster主要负责资源的申请，任务的分配




 **如果namenode高可用：**

	就没有secondaryNamenode了，取而代之的是journalNode：主要用于同步元数据信息，保证两个namenode的元数据信息是一致的
	并且journalNode需要奇数个，半数及以上的journalNode写入元数据成功，就代表写入成功
	nameNode  active状态：主要负责用户的写请求   
	nameNode  standBy状态：主要负责瞄着active什么时候死了，赶紧上位
	两个namenode组成主备的架构
 
#为什么要保证两个namenode看到的元数据信息是一样的？？？？ #

	nameNode  active状态： /hello/hello.txt
	nameNode  standBy状态： /hello/hello.txt
	集群的脑裂：为了避免集群的脑裂，造成看到的数据不一致，
	一定要保证两个namenode当中的元数据信息一模一样，journalnode就是同步两个namenode当中的元数据信息，
	保证两个namenode当中的元数据信息一模一样
	
	namenode高可用的自动切换，主要是通过两个守护进程zkfc来实现的


hadoop安装包结构
hadoop-2.7.5/bin:一些shell脚本，供我们使用
hadoop-2.7.5/sbin:一些shell脚本，供我们使用
hadoop-2.7.5/etc/hadoop:所有的配置文件的路径
hadoop-2.7.5/lib/native:本地的C程序库


检测hadoop的本地程序库
bin/hadoop checknative

hadoop:  true 本地库，支持我们使用C程序来访问hadoop
zlib:    true 压缩库
snappy:  false   压缩库  谷歌出品的一种压缩算法，谷歌出品，必属精品
lz4:     true  压缩库
bzip2:   false   压缩库


# hadoop 6个核心配置文件的作用： #

	core-site.xml：核心配置文件，主要定义了我们文件访问的格式  hdfs://
	hadoop-env.sh：主要配置我们的java路径
	hdfs-site.xml：主要定义配置我们的hdfs的相关配置
	mapred-site.xml  主要定义我们的mapreduce相关的一些配置
	slaves：控制我们的从节点在哪里 datanode   nodemanager在哪些机器上
	yarn-site.xml：配置我们的resourcemanager资源调度


**确定linux磁盘挂载的路径**

	df  -lh   查看linux的磁盘挂载路径
	/datadisk  1.9T的硬盘空间
	/mnt/resource   1.9T



	apache软件下载地址：
	http://archive.apache.org/dist/
	
	cdh所有软件下载地址：
	http://archive.cloudera.com/cdh5/cdh/5/



使用cdh的版本来进行安装，发现lib/natvie本地库没有。需要自己重新进行编译，让其支持本地库


集群搭建好了之后，第一次需要进行格式化，实际工作当中，以后都不要再格式化了，格式化之后所有的数据都没了


第一点：如何理解hdfs分布式文件系统，每台机器出一块磁盘，凑成一个大的硬盘，大的硬盘的容量来自各个服务器的硬盘容量之和


hdfs简介：全称是hadoop  distributed   file  system  数据存储的核心组件

hdfs的特性：
master/slave架构：namenode是我们的主节点，datanode是我们的从节点
				  namenode主要管理元数据信息  datanode主要存储各种数据

一次写入，多次读取：频繁的写入会造成元数据的频繁的更新改变，比较麻烦

# hdfs常用的操作命令 #

	hdfs  dfs   -ls  /  查看根路径下面的文件或者文件夹
	hdfs dfs  -mkdir  -p   /xx/xxx  在hdfs上面递归的创建文件夹
	hdfs  dfs -moveFromLocal  sourceDir(本地磁盘的文件或者文件夹的路径)   destDir（hdfs的路径）  
	hdfs  dfs  -mv  hdfsSourceDir   hdfsDestDir
	hdfs  dfs -put  localDir  hdfsDir   将本地文件系统的文件或者文件夹放到hdfs上面去
	hdfs  dfs -cat  hdfsDir 查看hdfs的文件内容
	hdfs  dfs  -cp   hdfsSourceDIr   hdfsDestDir   拷贝文件或者文件夹
	hdfs  dfs  -rm   [-r]  （递归）删除文件或者文件夹
	hdfs的权限管理两个命令：
	hdfs  dfs  -chmod -R  777  /xxx
	hdfs  dfs  -chown  -R hadoop:hadoop  /xxx



hdfs的高级命令使用：主要就是用于限制某个路径下面的文件的个数，或者限制某个路径下面文件的大小





hadoop的安全模式：hadoop集群刚启动的时候，默认处于安全模式30秒，安全模式的时候对外不提供给任何服务
刚开始启动的时候，处于安全模式：检测我们的datanode去了


测试写入速度；

hadoop jar /export/servers/hadoop-2.6.0-cdh5.14.0/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.6.0-cdh5.14.0.jar TestDFSIO  -write -nrFiles 10 -fileSize 10MB

写入速度一般在30M/S左右超不多

测试读取速度
hadoop jar /export/servers/hadoop-2.6.0-cdh5.14.0/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.6.0-cdh5.14.0.jar TestDFSIO -read -nrFiles 10 -fileSize 10MB
实际工作当中通过MR读取速度大概在100M/s左右





课程总结：
1、hadoop的简介发展历史
2、hadoop的历史版本
3、hadoop版本发行的三大公司 
	apache开源版本：
	hortonworks:
	cdh

4、hadoop1.x与2.x的架构介绍

2.x架构：hdfs，yarn  搞定
元数据：描述数据的数据


**hdfs集群: 端口 50070 **

		nameNode：主节点，主要用来维护元数据信息
	   dataNode:从节点，主要用于存储数据
	   secondaryNamenode：辅助namenode管理元数据信息
	   如果nameNode是HA  没有seconNameNode，有了journalNode
			journalNode:主要用于在两个namenode之间同步元数据信息，保证两个namenode的元数据信息一致

** yarn集群： 端口8088	**

		resourcemanager：主节点，接收用户的计算任务请求，分配资源
		    nodeManager：从节点  执行任务，执行appMaster给他分配的任务
			appMaster：全权负责我们任务的分配，资源的申请，任务的执行情况的汇报
			
jobHistory：查看所有的完成的历史任务的日志信息  19888		