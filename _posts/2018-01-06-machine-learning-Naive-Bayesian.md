---
layout: post
title: 机器学习笔记（三）之朴素贝叶斯法
category: blog
tags: [Machine Learning]
description: 机器学习笔记系列。
---


朴素贝叶斯法是基于贝叶斯原理与特征条件独立假设的分类方法。对于给定的训练数据集，首先基于特这条件独立假设学习输入输出的联合概率分布了然后基于此模型，对于给定的输入x，利用贝叶斯定理求出后验概率最大的输出y。朴素贝叶斯法实现简单，学习与预测的效率都和高，是一种常用的方法。

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/19.png?raw=true)

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/20.png?raw=true)


后验概率最大化含义：

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/21.png?raw=true)

学习与分类算法：
![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/22.png?raw=true)

扩展：极大似然估计和贝叶斯估计



极大似然估计与贝叶斯估计是统计中两种对模型的参数确定的方法，两种参数估计方法使用不同的思想。前者来自于频率派，认为参数是固定的，我们要做的事情就是根据已经掌握的数据来估计这个参数；而后者属于贝叶斯派，认为参数也是服从某种概率分布的，已有的数据只是在这种参数的分布下产生的。所以，直观理解上，极大似然估计就是假设一个参数 θ，然后根据数据来求出这个θ. 而贝叶斯估计的难点在于p(θ) 需要人为设定，之后再考虑结合MAP （maximum a posterior）方法来求一个具体的θ. 
所以极大似然估计与贝叶斯估计最大的不同就在于是否考虑了先验，而两者适用范围也变成了：极大似然估计适用于数据大量，估计的参数能够较好的反映实际情况；而贝叶斯估计则在数据量较少或者比较稀疏的情况下，考虑先验来提升准确率。

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/23.png?raw=true)
优点和缺点：



朴素贝叶斯的主要优点有：

　　　1）朴素贝叶斯模型发源于古典数学理论，有稳定的分类效率。

　　　2）对小规模的数据表现很好，能个处理多分类任务，适合增量式训练，尤其是数据量超出内存时，我们可以一批批的去增量训练。

　　　3）对缺失数据不太敏感，算法也比较简单，常用于文本分类。

朴素贝叶斯的主要缺点有：　　　

　　1） 理论上，朴素贝叶斯模型与其他分类方法相比具有最小的误差率。但是实际上并非总是如此，这是因为朴素贝叶斯模型给定输出类别的情况下,假设属性之间相互独立，这个假设在实际应用中往往是不成立的，在属性个数比较多或者属性之间相关性较大时，分类效果不好。而在属性相关性较小时，朴素贝叶斯性能最为良好。对于这一点，有半朴素贝叶斯之类的算法通过考虑部分关联性适度改进。

　　2）需要知道先验概率，且先验概率很多时候取决于假设，假设的模型可以有很多种，因此在某些时候会由于假设的先验模型的原因导致预测效果不佳。

　　3）由于我们是通过先验和数据来决定后验的概率从而决定分类，所以分类决策存在一定的错误率。

　　4）对输入数据的表达形式很敏感。





参考：《统计学习方法》-李航

参考：https://www.cnblogs.com/pinard/p/6069267.html

参考：http://blog.csdn.net/liu1194397014/article/details/52766760

参考：http://blog.csdn.net/liyajuan521/article/details/44565269
