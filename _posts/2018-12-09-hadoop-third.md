---
layout: post
title: hadoop原理(三)（Hadoop2.x版本）
category: blog
tags: [hadoop]
description: HBase原理及基本内容。
---


# 什么是HBase   
  
是一个分布式的、面向列的开源数据库，该技术来源于Fay Chang所撰写的Google论文“Bigtable：一个结构化数据的分布式存储系统”。就像Bigtable利用了Google文件系统（File System）所提供的分布式数据存储一样，HBase在Hadoop之上提供了类似于Bigtable的能力。HBase是Apache的Hadoop项目的子项目。HBase不同于一般的关系数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。  
  
HBase是一个针对结构化数据的可伸缩、高可靠、高性能、分布式和面向列的动态模式数据库。和传统关系数据库不同，HBase采用了BigTable的数据模型：增强的稀疏排序映射表（Key/Value），其中，键由行关键字、列关键字和时间戳构成。HBase提供了对大规模数据的随机、实时读写访问，同时，HBase中保存的数据可以使用MapReduce来处理，它将数据存储和并行计算完美地结合在一起。  

## 补充行数据库和列数据库区别

![HBase-2.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-2.png?raw=true)

## 列存储降低系统的I/O的原因

![HBase-3.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-3.png?raw=true)


# HBase架构

HBase采用Master/Slave架构搭建集群，它隶属于Hadoop生态系统，由一下类型节点组成：HMaster节点、HRegionServer节点、ZooKeeper集群，而在底层，它将数据存储于HDFS中，因而涉及到HDFS的NameNode、DataNode等，总体结构如下：
![HBase-1.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-1.png?raw=true)

## 其中HMaster节点用于：  

-  管理HRegionServer，实现其负载均衡    
-  管理和分配HRegion，比如在HRegion  
-  split时分配新的HRegion；在HRegionServer退出时迁移其内的HRegion到其他HRegionServer上。  
-  实现DDL操作（Data Definition Language，namespace和table的增删改，column familiy的增删改等）。  
-  管理namespace和table的元数据（实际存储在HDFS上）。  
-  权限控制（ACL）。    

## HRegionServer节点用于：   

-  存放和管理本地HRegion  
-  读写HDFS，管理Table中的数据   
-  Client直接通过HRegionServer读写数据（从HMaster中获取元数据，找到RowKey所在的HRegion/HRegionServer后）   

## ZooKeeper集群是协调系统，用于：    

-  存放整个 HBase集群的元数据以及集群的状态信息    
-  实现HMaster主从节点的failover 
-  Zookeeper是作为HBase Master的HA解决方案  



HBase Client通过RPC方式和HMaster、HRegionServer通信；一个HRegionServer可以存放1000个HRegion；底层Table数据存储于HDFS中，而HRegion所处理的数据尽量和数据所在的DataNode在一起，实现数据的本地化；数据本地化并不是总能实现，比如在HRegion移动(如因Split)时，需要等下一次Compact才能继续回到本地化。
-  1、存储超大文件  
   这里的“超大文件”是指几百MB、GB甚至TB级别的文件。  
-  2、最高效的访问模式是 一次写入、多次读取(流式数据访问)  
   HDFS存储的数据集作为hadoop的分析对象。在数据集生成后，长时间在此数据集上进行各种分析。每次分析都将设计该数据集的大部分数据甚至全部数据，因此读取整个数据集的时间延迟比读取第一条记录的时间延迟更重要。  
-  3、运行在普通廉价的服务器上  
   HDFS设计理念之一就是让它能运行在普通的硬件之上，即便硬件出现故障，也可以通过容错策略来保证数据的高可用。

# HBase存储模型

## 逻辑模型  

如图：
![HBase-4.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-4.png?raw=true)

HBase中的每一张表就是所谓的BigTable。BigTable会存储一系列的行记录，行记录有三个基本类型的定义：
RowKey：与nosql数据库们一样,row key是用来检索记录的主键。访问Hbase table中的行，只有三种方式：
-  1.通过单个row key访问

-  2.通过row key的range

-  3.全表扫描    
 
Row key行键 (Row key):   可以是任意字符串最大长度是64KB，实际应用中长度一般为10-100bytes),在Hbase内部，row key保存为字节数组。存储时，数据按照Row key的字典序(byte order)排序存储。设计key时，要充分排序存储这个特性，将经常一起读取的行存储放到一起。(位置相关性)   
注意：字典序对int排序的结果是1,10,100,11,12,13,14,15,16,17,18,19,2,20,21,…,9,91,92,93,94,95,96,97,98,99。要保持整形的自然序，行键必须用0作左填充。       
行的一次读写是原子操作,不论一次读写多少列。这个设计决策能够使用户很容易的理解程序在对同一个行进行并发更新操作时的行为。    
  
Column：   
Hbase表中的每个列，都归属与某个列族。列族是表的chema的一部分(而列不是)，必须在使用表之前定义。列名都以列族作为前缀。例如courses:history，courses:math  

都属于courses 这个列族。   

访问控制、磁盘和内存的使用统计都是在列族层面进行的。实际应用中，列族上的控制权限能帮助我们管理不同类型的应用：我们允许一些应用可以添加 新的基本数据、一些应用可以读取基本数据并创建继承的列族、一些应用则只允许浏览数据(甚至可能因为隐私的原因不能浏览所有数据)。   

时间戳：

Hbase中通过row和columns确定的为一个存贮单元称为cell。每个 cell都保存着同一份数据的多个版本。版本通过时间戳来索引。时间戳的类型是 64位整型。时间戳可以由Hbase(在数据写入时自动)赋值，此时时间戳是精确到毫秒的当前系统时间。时间戳也可以由客户显式赋值。如果应用程序要避免数据版本冲突，就必须自己生成具有唯一性的时间戳。每个 cell中，不同版本的数据按照时间倒序排序，即最新的数据排在最前面。  

为了避免数据存在过多版本造成的的管理 (包括存贮和索引)负担，Hbase提供了两种数据版本回收方式。一是保存数据的最后n个版本，二是保存最近一段时间内的版本(比如最近七天)。用户可以针对每个列族进行设置。    

## 物理模型  

-  表在行的方向上分割为多个Region
![HBase-5.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-5.png?raw=true)
-  region按大小分割的，每个表一开始只有一个region，随着数据不断插入表，region不断增大，当增大到一个阀值的时候，Hregion就会等分会两个新的Hregion。当table中的行不断增多，就会有越来越多的Hregion。
![HBase-6.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-6.png?raw=true)
-  Region是Hbase中分布式存储和负载均衡的最小单元，不同Region分布到不同RegionServer上

注意：HRegion虽然是分布式存储的最小单元，但并不是存储的最小单元。事实上，HRegion由一个或者多个Store组成，每个store保存一个columns family。每个Strore又由一个memStore和0至多个StoreFile组成。如图：StoreFile以HFile格式保存在HDFS上。

## ROOT表和META表

HBase的所有Region元数据被存储在.META.表中，随着Region的增多，.META.表中的数据也会增大，并分裂成多个新的Region。为了定位.META.表中各个Region的位置，把.META.表中所有Region的元数据保存在-ROOT-表中，最后由Zookeeper记录-ROOT-表的位置信息。所有客户端访问用户数据前，需要首先访问Zookeeper获得-ROOT-的位置，然后访问-ROOT-表获得.META.表的位置，最后根据.META.表中的信息确定用户数据存放的位置，如下图所示。

![HBase-8.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-8.png?raw=true)

-ROOT-表永远不会被分割，它只有一个Region，这样可以保证最多只需要三次跳转就可以定位任意一个Region。为了加快访问速度，.META.表的所有Region全部保存在内存中。客户端会将查询过的位置信息缓存起来，且缓存不会主动失效。如果客户端根据缓存信息还访问不到数据，则询问相关.META.表的Region服务器，试图获取数据的位置，如果还是失败，则询问-ROOT-表相关的.META.表在哪里。最后，如果前面的信息全部失效，则通过ZooKeeper重新定位Region的信息。所以如果客户端上的缓存全部是失效，则需要进行6次网络来回，才能定位到正确的Region。

# HBase读写流程

如图：
![HBase-7.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/HBase-7.png?raw=true)

## 写操作流程

-  (1) Client通过Zookeeper的调度，向RegionServer发出写数据请求，在Region中写数据  
-  (2) 数据被写入Region的MemStore，直到MemStore达到预设阈值    
-  (3) MemStore中的数据被Flush成一个StoreFile   
-  (4) 随着StoreFile文件的不断增多，当其数量增长到一定阈值后，触发Compact合并操作，将多个StoreFile合并成一个StoreFile，同时进行版本合并和数据删除   
-  (5) StoreFiles通过不断的Compact合并操作，逐步形成越来越大的StoreFile  
-  (6) 单个StoreFile大小超过一定阈值后，触发Split操作，把当前Region Split成2个新的Region。父Region会下线，新Split出的2个子Region会被HMaster分配到相应的RegionServer上，使得原先1个Region的压力得以分流到2个Region上   

可以看出HBase只有增添数据，所有的更新和删除操作都是在后续的Compact历程中举行的，使得用户的写操作只要进入内存就可以立刻返回，实现了HBase I/O的高机能。

## 读操作流程

-  (1) Client访问Zookeeper，查找-ROOT-表，获取.META.表信息   
-  (2) 从.META.表查找，获取存放目标数据的Region信息，从而找到对应的RegionServer   
-  (3) 通过RegionServer获取需要查找的数据   
-  (4) Regionserver的内存分为MemStore和BlockCache两部分，MemStore主要用于写数据，BlockCache主要用于读数据。读请求先到MemStore中查数据，查不到就到BlockCache中查，再查不到就会到StoreFile上读，并把读的结果放入BlockCache  

**参考资料**

<https://www.cnblogs.com/cxzdy/p/5046820.html>
<https://www.cnblogs.com/csyuan/p/6543018.html>
