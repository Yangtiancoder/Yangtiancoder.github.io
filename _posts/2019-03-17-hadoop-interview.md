---
layout: post
title: 大数据研发面试记录
category: blog
tags: [hadoop]
description: 平安-金融数据组,ebay-大数据搜索组。
---


前言：打算背着老板偷偷出去实习，虽然老板开学就说过，偷偷实习就逐出师门...所以我室友也说我将是第一个被逐出师门的人了。。。胆子大，现在还是研一，感觉研二要被压榨了，更不可能出去了，趁着活少赶紧出去，只能去偷偷投日常实习的岗位，感觉貌似也不算很难，刷题之类的，准备也有小半年的了，就是简历投出去了好几份，但是貌似收简历的不多，很多大企业简历实习僧上投出去HR都没看过，真是这点面子都不给的，现在正值春招，可能都不缺打杂的吧，上周面了两天，平安金融是一账通研发团队的金融数据组，大数据实习岗位，面试比较简单，线下去了一上午，三次面试连着面完了，下午就通知办理入职之类的，感觉各种服务还是蛮到位的，业务也比较正规，ebay应该是搜索业务部门，工程师直接邮件联系的我，进行线上面试，感觉面试还是蛮难的，现在记录一下面试内容。

## 平安
大概前两面是技术面，第三面那个人感觉懂点技术又感觉他不懂，记录了面试的问题如下：  
jvm模型：常规不赘述   
gc算法，过程：常规不赘述    
hashmap数据结构原理：答了两个版本的，扯扯源码  
hashmap线程安全性：hashmap死循环，扯源码  
hashtable和cocurrenthashmap区别 ： cocurrenthashmap扯两个版本+扯源码   
到这里问我是不经常看源码...虽然我只看过几个集合类的...但是我说学习过程中遇到难点实现先从源码看起，吹逼成功。  
Overload和Override的区别：常规不赘述   
hashCode和equals方法的关系：常规不赘述     
spark，flink区别：常规不赘述   
hbase的rowkey设计注意：这个我真没注意...回去查了一下...   
MR过程：常规不赘述   
spark进行了哪些优化:详细一点shuffle优化,consolidation机制，DAG，宽窄依赖等等都讲   
hive调优：我之前做过hive调优的PJ...   
zookeeper为什么三台选举：常规不赘述     

知识点都简单基础，几乎都没深问...还有一些半技术半非技术的问题，感觉还是很水的，还有一些针对项目的问题，我的项目一个是基于spark一个是基于flink，基本生态都有用到，会问一下处理流程乱序处理等问题。
基本知识点的都有准备，面试比较顺畅，本来一面完我已经走了，告诉我一周内出结果，结果过几分钟就给我叫回来了，然后让我等着等着我就面完了，目测对我比较满意，可能也是缺人的原因，下午发了offer，不出意外的话，过几天会入职这里，感觉环境还是蛮不错的，不知道技术氛围怎么样。

## ebay

ebay是工程师直接联系的我，面试过程感觉比较难，好多业务场景我没经历过，感觉自己完全在胡说，一直被面试官提醒，感觉那个工程师的技术还是不错的，善于引导，听了一下业务也比较感兴趣，周五下午面完，刚好等一个周末，现在还没消息，但是我觉得我整个过程普普通通，不好不坏，估计是凉了。

话不多说上来两道算法：
第一道算是图的吧  
输入类似
2 3    
1 6    
2 5    
4 7    
告诉你有7个小朋友，23，代表编号为2，3的小朋友认识，包含认识的小朋友在一个圈，上题也就是2，一共两个圈，（1,2,3,5,6）（4,7）输出2。
当时脑袋发懵，一开始总想着hashmap结构就跑偏了，想了不对，后来觉得hashset可行，说了解题思路可行但是面试官说太heavy，告诉我可以想象成点，我反应了一下才想到23表示边的关系，也没想到并查集，说了简化为矩阵数组置1二维下标表示边，判断有几个连通图，但是dfs忘记了怎么搞，触及到知识盲区...面试官就换了下一道...
第二题反转链表...
刚刷过没多久，2分钟搞定...面试官告诉我注意边界等等问题，可能他没想到这么快...我看我边界没问题，就说没问题，然后面试官测试一下，模拟一下验证了我的思路正确性，code到此结束。

然后场景题，我要死了，这部分讨论了一个多小时...类似于让你设计一个系统（面试官说的场景很复杂，总之就是喜欢不直接告诉你问题。。。意思是你自己提炼去），简言之可以处理客户端的请求串，类似于hash处理并返回，处理逻辑很简单，问你如何设计这个系统...我一听一脸懵逼...太宽泛了不知从何说起（内省OS：我又没实际应用过这些我哪会啊...）。
我刚开始说的是，逻辑简单，可以开线程池，然后kafka吞吐比较高，类似于消息队列，处理之后数据可以放到redis中也可以持久化，比较安全，也可以定期删除，因为业务场景数据不需要持久存储，然后说这样单台机器可靠性怎么保证，我就提了一个类似于master-slave结构，得到了面试官的肯定，然后又问多台机器处理不同步处理怎么办，然后我提了一条可以单台机器处理日志信息，然后同步到各个机器，又问到日志机器宕机了怎么办（我说的类似于Secondary NameNode），我就跑偏了，说日志也集群...也会有同步问题...然后面试官问我知道hadoopHA么，我才想起来，分为状态管理和日志同步，讲起了HA那一大套，什么防止脑裂啊journalnode之类的往上扯,然后面试官问有没有可以借鉴的..我说当然。。。肯定借鉴日志处理机制啊...balabala，然后后面又问了一些场景，单台机器处理能力上限之类的，现在想想应该是想考我yarn机制原理，hadoop两个版本我都很熟但是这种问法当时没想到...后面又扯到了zookeeper等等,想不起来了...大概诸如此类的，好多我不太会，因为我没有过处理实际数据场景的经验，我就靠猜面试官的意思...感觉自己在乱说，还得靠面试官引导才能答出来...然后结束了。问我想问他什么没有，我说什么时候能知道结果，他说不是他决定的，他还要和monitor商量一下，好的...结束

整场面试下来一个基础没问...主要是后面实际应用部分吧，说实话我就是靠猜...这个面试官不喜欢直接问你，喜欢引导你，大概原理他默认你会了就看你能不能自己想起来...还是我太菜了，不能触类旁通，大家引以为戒，目测不会继续面试了，这个杯具了我就先去第一个企业，这个企业我也蛮喜欢的，积累一下实习经历再次出击。

加油，老田冲鸭！  

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/cat.png?raw=true)




