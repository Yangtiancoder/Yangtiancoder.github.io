---
layout: post
title: hadoop原理(四)（Hadoop2.x版本）
category: blog
tags: [hadoop]
description: Zookeeper原理及作用。
---


# 什么是Zookeeper
  
ZooKeeper顾名思义动物园管理员，他是拿来管大象(Hadoop)、蜜蜂(Hive)、小猪(Pig)的管理员，ApacheHbase和ApacheSolr以及LinkedInsensei等项目中都采用到了Zookeeper。ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，ZooKeeper是以FastPaxos算法为基础，实现同步服务，配置维护和命名服务等分布式应用。    

上面的解释感觉还不够，太官方了。Zookeeper 从程序员的角度来讲可以理解为Hadoop的整体监控系统。如果namenode,HMaster宕机后，这时候Zookeeper 的重新选出leader。这是它最大的作用所在。    

# zookeeper的作用及其架构

ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，它包含一个简单的原语集，分布式应用程序可以基于它实现同步服务，配置维护和命名服务等。Zookeeper是hadoop的一个子项目，在分布式应用中，由于工程师不能很好地使用锁机制，以及基于消息的协调机制不适合在某些应用中使用，因此需要有一种可靠的、可扩展的、分布式的、可配置的协调机制来统一系统的状态。Zookeeper的目的就在于此。

![Zookeeper-1.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-1.png?raw=true)

ZooKeeper集群由一组Server节点组成，这一组Server节点中存在一个角色为Leader的节点，其他节点都为Follower。当客户端Client连接到ZooKeeper集群，并且执行写请求时，这些请求会被发送到Leader节点上，然后Leader节点上数据变更会同步到集群中其他的Follower节点。  
Leader节点在接收到数据变更请求后，首先将变更写入本地磁盘，以作恢复之用。当所有的写请求持久化到磁盘以后，才会将变更应用到内存中。   
ZooKeeper使用了一种自定义的原子消息协议，在消息层的这种原子特性，保证了整个协调系统中的节点数据或状态的一致性。Follower基于这种消息协议能够保证本地的ZooKeeper数据与Leader节点同步，然后基于本地的存储来独立地对外提供服务。
当一个Leader节点发生故障失效时，失败故障是快速响应的，消息层负责重新选择一个Leader，继续作为协调服务集群的中心，处理客户端写请求，并将ZooKeeper协调系统的数据变更同步（广播）到其他的Follower节点。  

# Zookeeper设计目的

-  最终一致性：client不论连接到哪个Server，展示给它都是同一个视图，这是zookeeper最重要的性能。 


-  可靠性：具有简单、健壮、良好的性能，如果消息被到一台服务器接受，那么它将被所有的服务器接受。 


-  实时性：Zookeeper保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。但由于网络延时等原因，Zookeeper不能保证两个客户端能同时得到刚更新的数据，如果需要最新数据，应该在读数据之前调用sync()接口。 


-  等待无关（wait-free）：慢的或者失效的client不得干预快速的client的请求，使得每个client都能有效的等待。 


-  原子性：更新只能成功或者失败，没有中间状态。 


-  顺序性：包括全局有序和偏序两种：全局有序是指如果在一台服务器上消息a在消息b前发布，则在所有Server上消息a都将在消息b前被发布；偏序是指如果一个消息b在消息a后被同一个发送者发布，a必将排在b前面。 


# Zookeeper工作原理

Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。
-  当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和 leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态   
-  当 ZooKeeper 集群选举出 leader 同步完状态退出恢复模式之后，便进入了原子广播模式。 所有的写请求都被转发给 leader，再由 leader 将更新 proposal 广播给 follower  

为了保证事务的顺序一致性，zookeeper采用了递增的事务id号（zxid）来标识事务。所有的提议（proposal）都在被提出的时候加上了zxid。实现中zxid是一个64位的数字，它高32位是epoch用来标识leader关系是否改变，每次一个leader被选出来，它都会有一个 新的epoch，标识当前属于那个leader的统治时期。低32位用于递增计数。

每个Server在工作过程中有三种状态：

-  LOOKING：当前Server不知道leader是谁，正在搜寻   

-  LEADING：当前Server即为选举出来的leader   

-  FOLLOWING：leader已经选举出来，当前Server与之同步  

## 选主流程    

当leader崩溃或者leader失去大多数的follower，这时候zk进入恢复模式，恢复模式需要重新选举出一个新的leader，让所有的Server都恢复到一个正确的状态。Zk的选举算法有两种：一种是基于basic paxos实现的，另外一种是基于fast paxos算法实现的。系统默认的选举算法为fast paxos。先介绍basic paxos流程：    

-  1.选举线程由当前Server发起选举的线程担任，其主要功能是对投票结果进行统计，并选出推荐的Server     

-  2.选举线程首先向所有Server发起一次询问(包括自己)  

-  3.选举线程收到回复后，验证是否是自己发起的询问(验证zxid是否一致)，然后获取对方的id(myid)，并存储到当前询问对象列表中，最后获取对方提议的leader相关信息(id,zxid)，并将这些信息存储到当次选举的投票记录表中    

-  4.收到所有Server回复以后，就计算出zxid最大的那个Server，并将这个Server相关信息设置成下一次要投票的Server     

-  5.线程将当前zxid最大的Server设置为当前Server要推荐的Leader，如果此时获胜的Server获得n/2+1的Server票数，设置当前推荐的leader为获胜的Server，将根据获胜的Server相关信息设置自己的状态，否则，继续这个过程，直到leader被选举出来  
 
通过流程分析我们可以得出：要使Leader获得多数Server的支持，则Server总数必须是奇数2n+1，且存活的Server的数目不得少于n+1    

每个Server启动后都会重复以上流程。在恢复模式下，如果是刚从崩溃状态恢复的或者刚启动的server还会从磁盘快照中恢复数据和会话信息，zk会记录事务日志并定期进行快照，方便在恢复时进行状态恢复。选主的具体流程图如下所示：  

![Zookeeper-2.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-2.png?raw=true)  

fast paxos流程是在选举过程中，某Server首先向所有Server提议自己要成为leader，当其它Server收到提议以后，解决epoch和zxid的冲突，并接受对方的提议，然后向对方发送接受提议完成的消息，重复这个流程，最后一定能选举出Leader。其流程图如下所示：  

![Zookeeper-3.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-3.png?raw=true)  

## 同步流程

选完leader以后，zk就进入状态同步过程  

-  1.leader等待server连接   

-  2.Follower连接leader，将最大的zxid发送给leader   

-  3.Leader根据follower的zxid确定同步点   

-  4.完成同步后通知follower 已经成为uptodate状态   
 
-  5.Follower收到uptodate消息后，又可以重新接受client的请求进行服务了    

流程图如下所示：

![Zookeeper-4.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-4.png?raw=true)     

## 工作流程

### Leader工作流程

Leader主要有三个功能：  

-  1.恢复数据  

-  2.维持与Learner的心跳，接收Learner请求并判断Learner的请求消息类型   

-  3.Learner的消息类型主要有PING消息、REQUEST消息、ACK消息、REVALIDATE消息，根据不同的消息类型，进行不同的处理   

PING消息是指Learner的心跳信息；REQUEST消息是Follower发送的提议信息，包括写请求及同步请求；ACK消息是Follower的对提议的回复，超过半数的Follower通过，则commit该提议；REVALIDATE消息是用来延长SESSION有效时间。
Leader的工作流程简图如下所示，在实际实现中，流程要比下图复杂得多，启动了三个线程来实现功能。  

![Zookeeper-5.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-5.png?raw=true)      

### Follower工作流程  

Follower主要有四个功能：  

-  1.向Leader发送请求（PING消息、REQUEST消息、ACK消息、REVALIDATE消息）   

-  2.接收Leader消息并进行处理  

-  3.接收Client的请求，如果为写请求，发送给Leader进行投票  

-  4.返回Client结果  

Follower的消息循环处理如下几种来自Leader的消息：  

-  1.PING消息： 心跳消息  

-  2.PROPOSAL消息：Leader发起的提案，要求Follower投票  

-  3.COMMIT消息：服务器端最新一次提案的信息   

-  4.UPTODATE消息：表明同步完成       

-  5.REVALIDATE消息：根据Leader的REVALIDATE结果，关闭待revalidate的session还是允许其接受消息   

-  6.SYNC消息：返回SYNC结果到客户端，这个消息最初由客户端发起，用来强制得到最新的更新   

Follower的工作流程简图如下所示，在实际实现中，Follower是通过5个线程来实现功能的。  

![Zookeeper-6.png](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/Zookeeper-6.png?raw=true)      

对于observer的流程不再叙述，observer流程和Follower的唯一不同的地方就是observer不会参加leader发起的投票。   

# 应用场景

ZooKeeper是一个高可用的分布式数据管理与系统协调框架。基于对Paxos算法的实现，使该框架保证了分布式环境中数据的强一致性，也正是基 于这样的特性，使得zookeeper能够应用于很多场景。

## 数据发布与订阅

发布与订阅即所谓的配置管理，顾名思义就是将数据发布到zk节点上，供订阅者动态获取数据，实现配置信息的集中式管理和动态更新。例如全局的配置信息，地址列表等就非常适合使用。  

-  1.索引信息和集群中机器节点状态存放在zk的一些指定节点，供各个客户端订阅使用  
-  2.系统日志（经过处理后的）存储，这些日志通常2-3天后被清除  
-  3.应用中用到的一些配置信息集中管理，在应用启动的时候主动来获取一次，并且在节点上注册一个Watcher，以后每次配置有更新，实时通知到应用，获取最新配置信息  
-  4.业务逻辑中需要用到的一些全局变量，比如一些消息中间件的消息队列通常有个offset，这个offset存放在zk上，这样集群中每个发送者都能知道当前的发送进度  
-  5.系统中有些信息需要动态获取，并且还会存在人工手动去修改这个信息。以前通常是暴露出接口，例如JMX接口，有了zk后，只要将这些信息存放到zk节点上即可  

## 分布通知/协调

ZooKeeper 中特有watcher注册与异步通知机制，能够很好的实现分布式环境下不同系统之间的通知与协调，实现对数据变更的实时处理。使用方法通常是不同系统都对ZK上同一个znode进行注册，监听znode的变化（包括znode本身内容及子节点的），其中一个系统update了znode，那么另一个系统能 够收到通知，并作出相应处理   

-  1.另一种心跳检测机制：检测系统和被检测系统之间并不直接关联起来，而是通过zk上某个节点关联，大大减少系统耦合  
-  2.另一种系统调度模式：某系统有控制台和推送系统两部分组成，控制台的职责是控制推送系统进行相应的推送工作。管理人员在控制台作的一些操作，实际上是修改了ZK上某些节点的状态，而zk就把这些变化通知给他们注册Watcher的客户端，即推送系统，于是，作出相应的推送任务   
-  3.另一种工作汇报模式：一些类似于任务分发系统，子任务启动后，到zk来注册一个临时节点，并且定时将自己的进度进行汇报（将进度写回这个临时节点），这样任务管理者就能够实时知道任务进度   

总之，使用zookeeper来进行分布式通知和协调能够大大降低系统之间的耦合。  


## 分布式锁

分布式锁，这个主要得益于ZooKeeper为我们保证了数据的强一致性，即用户只要完全相信每时每刻，zk集群中任意节点（一个zk server）上的相同znode的数据是一定是相同的。锁服务可以分为两类，一个是保持独占，另一个是控制时序。
保持独占，就是所有试图来获取这个锁的客户端，最终只有一个可以成功获得这把锁。通常的做法是把zk上的一个znode看作是一把锁，通过create znode的方式来实现。所有客户端都去创建 /distribute_lock 节点，最终成功创建的那个客户端也即拥有了这把锁。   
控制时序，就是所有视图来获取这个锁的客户端，最终都是会被安排执行，只是有个全局时序了。做法和上面基本类似，只是这里 /distribute_lock 已经预先存在，客户端在它下面创建临时有序节点（这个可以通过节点的属性控制：CreateMode.EPHEMERAL_SEQUENTIAL来指定）。Zk的父节点（/distribute_lock）维持一份sequence,保证子节点创建的时序性，从而也形成了每个客户端的全局时序。  


## 集群管理

-  1.集群机器监控：这通常用于那种对集群中机器状态，机器在线率有较高要求的场景，能够快速对集群中机器变化作出响应。这样的场景中，往往有一个监控系统，实时检测集群机器是否存活。过去的做法通常是：监控系统通过某种手段（比如ping）定时检测每个机器，或者每个机器自己定时向监控系统汇报“我还活着”  
这种做法可行，但是存在两个比较明显的问题：   
    -  1.集群中机器有变动的时候，牵连修改的东西比较多   
    -  2.有一定的延时   

利用ZooKeeper有两个特性，就可以实时另一种集群机器存活性监控系统：
    a.客户端在节点 x 上注册一个Watcher，那么如果 x 的子节点变化了，会通知该客户端   
    b.创建EPHEMERAL类型的节点，一旦客户端和服务器的会话结束或过期，那么该节点就会消失   

-  2.Master选举则是zookeeper中最为经典的使用场景了  
在分布式环境中，相同的业务应用分布在不同的机器上，有些业务逻辑（例如一些耗时的计算，网络I/O处理），往往只需要让整个集群中的某一台机器进行执行，其余机器可以共享这个结果，这样可以大大减少重复劳动，提高性能，于是这个master选举便是这种场景下的碰到的主要问题。
利用ZooKeeper的强一致性，能够保证在分布式高并发情况下节点创建的全局唯一性，即：同时有多个客户端请求创建 /currentMaster 节点，最终一定只有一个客户端请求能够创建成功。   



**参考资料**

<https://www.cnblogs.com/felixzh/p/5869212.html>  
<https://blog.csdn.net/wzk646795873/article/details/79706627>

