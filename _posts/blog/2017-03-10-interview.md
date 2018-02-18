---
layout:     post
title: 前车之鉴
category: blog
description: java面试经验
---
                                                                                                      

春招的话金三银四，要好好把握，这是我之面试中总结的知识点，一部分是网上down来的，一部分是搜集的面试问题，应该是很全面了，问来问去基础也就那些，无论是BAT等一线大厂或者其他不同的企业这些面试题目足够了，再也有一些推荐的书单这个大家可以在网上找然后自行下载和也附上我的一些资料，具体的学习路线不用多说，自行Baidu即可，弄懂了这些面试常见问题，基础的方面算是解决了，剩下的项目经验还要注重平时积累，切忌临时抱佛脚，很快就会露馅的。

网站推荐

http://gityuan.com/2016/01/09/java-memory/内存模型
http://www.guokr.com/post/114121/ Https握手
http://blog.csdn.net/laner0515/article/details/27692673/核心工作原理
http://www.cnblogs.com/fanzhidongyzby/p/4098546.html IO
http://blog.csdn.net/sszgg2006/article/details/38664789 IO
http://blog.csdn.net/forfuture3513/article/details/52445213 IO
http://www.blogjava.net/bolo/archive/2015/01/20/422296.html 并发模型
http://blog.csdn.net/qq396229783/article/details/19924393 String
http://jingyan.baidu.com/article/acf728fd2182e7f8e510a32e.html 中文编码问题
http://www.cnblogs.com/200911/p/3965108.html 内存溢出解决方案
https://www.nowcoder.com/discuss/5683 spring问题
http://blog.csdn.net/u010425776/article/details/55006347 计算机网络
http://blog.csdn.net/hguisu/article/details/7445768/ Socket
http://www.jb51.net/article/19024.htm SQL优化
http://www.cnblogs.com/coder2012/p/5309197.html 索引
http://www.360doc.com/content/14/0903/10/15077656_406704297.shtml redis设计与实现
http://blog.csdn.net/sinat_35512245/article/details/59056120 面试题总结网址




面试题汇总

1. 九种基本数据类型的大小，以及他们的封装类。
2. Switch能否用string做参数？
3. equals与==的区别。
4. Object有哪些公用方法？
5. Java的四种引用，强弱软虚，用到的场景
6. Hashcode的作用。
7. ArrayList、LinkedList、Vector的区别。
8. String、StringBuffer与StringBuilder的区别。
9. Map、Set、List、Queue、Stack的特点与用法。
10. HashMap和HashTable的区别。
11. HashMap和ConcurrentHashMap的区别，HashMap的底层源码。
12. TreeMap、HashMap、LindedHashMap的区别。
13. Collection包结构，与Collections的区别。
14. try catch finally，try里有return，finally还执行么？
15. Excption与Error包结构。OOM你遇到过哪些情况，SOF你遇到过哪些情况。
16. Java面向对象的三个特征与含义。
17. Override和Overload的含义去区别。
18. Interface与abstract类的区别。
19. Static class 与non static class的区别。
20. java多态的实现原理。
21. 实现多线程的两种方法：Thread与Runable。
22. 线程同步的方法：sychronized、lock、reentrantLock等。
23. 锁的等级：方法锁、对象锁、类锁。
24. 写出生产者消费者模式。
25. ThreadLocal的设计理念与作用。
26. ThreadPool用法与优势。
27. Concurrent包里的其他东西：ArrayBlockingQueue、CountDownLatch等等。
28. wait()和sleep()的区别。
29. foreach与正常for循环效率对比。
30. Java IO与NIO。
31. 反射的作用于原理。
32. 泛型常用特点，List<String>能否转为List<Object>。
33. 解析XML的几种方式的原理与特点：DOM、SAX、PULL。
34. Java与C++对比。
35. Java1.7与1.8新特性。
36. 设计模式：单例、工厂、适配器、责任链、观察者等等。
37. JNI的使用。
JVM
1. 内存模型以及分区，需要详细到每个区放什么。
2. 堆里面的分区：Eden，survival from to，老年代，各自的特点。
3. 对象创建方法，对象的内存分配，对象的访问定位。
4. GC的两种判定方法：引用计数与引用链。
5. GC的三种收集方法：标记清除、标记整理、复制算法的原理与特点，分别用在什么地方，如果让你优化收集方法，有什么思路？
6. GC收集器有哪些？CMS收集器与G1收集器的特点。
7. Minor GC与Full GC分别在什么时候发生？
8. 几种常用的内存调试工具：jmap、jstack、jconsole。
9. 类加载的五个过程：加载、验证、准备、解析、初始化。
10. 双亲委派模型：Bootstrap ClassLoader、Extension ClassLoader、ApplicationClassLoader。
11. 分派：静态分派与动态分派。
操作系统
1. 进程和线程的区别。
2. 死锁的必要条件，怎么处理死锁。
3. Window内存管理方式：段存储，页存储，段页存储。
4. 进程的几种状态。
5. IPC几种通信方式。
6. 什么是虚拟内存。
7. 虚拟地址、逻辑地址、线性地址、物理地址的区别。
TCP/IP
1. OSI与TCP/IP各层的结构与功能，都有哪些协议。
2. TCP与UDP的区别。
3. TCP报文结构。
4.TCP的三次握手与四次挥手过程，各个状态名称与含义，TIMEWAIT的作用。
5. TCP拥塞控制
6. TCP滑动窗口与回退N针协议。
7. Http的报文结构。
8. Http的状态码含义。
9. Http request的几种类型。
10. Http1.1和Http1.0的区别
11. Http怎么处理长连接。
12. Cookie与Session的作用于原理。
13. 电脑上访问一个网页，整个过程是怎么样的：DNS、HTTP、TCP、OSPF、IP、ARP。
14. Ping的整个过程。ICMP报文是什么。
15. C/S模式下使用socket通信，几个关键函数。
16. IP地址分类。
17. 路由器与交换机区别。
数据结构与算法
1. 链表与数组。
2. 队列和栈，出栈与入栈。
3. 链表的删除、插入、反向。
4. 字符串操作。
5. Hash表的hash函数，冲突解决方法有哪些。
6. 各种排序：冒泡、选择、插入、希尔、归并、快排、堆排、桶排、基数的原理、平均时间复杂度、最坏时间复杂度、空间复杂度、是否稳定。
7. 快排的partition函数与归并的Merge函数。
8. 对冒泡与快排的改进。
9. 二分查找，与变种二分查找。
10. 二叉树、B+树、AVL树、红黑树、哈夫曼树。
11. 二叉树的前中后续遍历：递归与非递归写法，层序遍历算法。
12. 图的BFS与DFS算法，最小生成树prim算法与最短路径Dijkstra算法。
13. KMP算法。
14. 排列组合问题。
15. 动态规划、贪心算法、分治算法。（一般不会问到）
16. 大数据处理：类似10亿条数据找出最大的1000个数.........等等
面向对象和面向过程的区别
面试问题（来自BAT等企业）:

IO模型有哪些？
进程线程的区别
不同操作系统实现进程有什么区别
gc算法，回收器有哪些
ACID，事务隔离机制
syn在方法上和代码块有什么不同
memcached和其他nosql的区别
解释mvc
threadlocal解释
volatile的作用
堆和栈的区别和联系
tcp和udp的不同之处
tcp如何保证可靠的
数组和链表的区别
排序算法应用场景
lucene全文检索原理

动态规划
spring事务
内存线程隔离
sleep()wait
三次握手两次回收
S1和S2
RuntimeException
HashMap中hash
future
Callable
clone
负载因子
二叉树遍历
vector
回溯法
 B-Tree 与B+Tree
struts和spring
HttpServlet容器响应Web客户请求流程如下：
1）Web客户向Servlet容器发出Http请求；
2）Servlet容器解析Web客户的Http请求；
3）Servlet容器创建一个HttpRequest对象，在这个对象中封装Http请求信息；
4）Servlet容器创建一个HttpResponse对象；
5）Servlet容器调用HttpServlet的service方法，这个方法中会根据request的Method来判断具体是执行doGet还是doPost，把HttpRequest和HttpResponse对象作为service方法的参数传给HttpServlet对象；
6）HttpServlet调用HttpRequest的有关方法，获取HTTP请求信息；
7）HttpServlet调用HttpResponse的有关方法，生成响应数据；
8）Servlet容器把HttpServlet的响应结果传给Web客户。

doGet() 或 doPost() 是创建HttpServlet时需要覆盖的方法.
java.util.concourrent
join
链表相交
判断回文字串
spring和struts
词频统计

集合类以及集合框架；HashMap与HashTable实现原理，线程安全性，hash冲突及处理算法；ConcurrentHashMap

进程和线程的区别；多线程与线程池


数据一致性如何保证；Synchronized关键字，类锁，方法锁，重入锁


同步的方法；多进程开发以及多进程应用场景


服务器只提供数据接收接口，在多线程或多进程条件下，如何保证数据的有序到达


ThreadLocal原理，实现及如何保证Local属性


String StringBuilder StringBuffer对比


接口与回调；回调的原理；写一个回调demo；


泛型原理，举例说明；解析与分派


抽象类与接口的区别；应用场景；抽象类是否可以没有方法和属性


静态属性和静态方法是否可以被继承？是否可以被重写？原因


修改对象A的equals方法的签名，那么使用HashMap存放这个对象实例的时候，会调用哪个equals方法


数据结构与算法


堆和栈在内存中的区别是什么(数据结构方面以及实际实现方面)


最快的排序算法是哪个？给阿里2万多名员工按年龄排序应该选择哪个算法？堆和树的区别；写出快排代码；链表逆序代码


求1000以内的水仙花数以及40亿以内的水仙花数


子串包含问题(KMP 算法)写代码实现


万亿级别的两个URL文件A和B，如何求出A和B的差集C,(Bit映射->hash分组->多文件读写效率->磁盘寻址以及应用层面对寻址的优化)

写出你所知道的排序算法及时空复杂度，稳定性


百度POI中如何试下查找最近的商家功能(坐标镜像+R树)
死锁的四个必要条件


常见编码方式；utf-8编码中的中文占几个字节；int型几个字节


实现一个Json解析器(可以通过正则提高速度)


MVC MVP MVVM; 常见的设计模式；写出观察者模式的代码


TCP的3次握手和四次挥手；TCP与UDP的区别


HTTP协议；HTTP1.0与2.0的区别；HTTP报文结构




