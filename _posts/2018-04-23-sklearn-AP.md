---
layout: post
title: sklearn使用小结
category: blog
tags: [机器学习,聚类,毕设]
description: sklearn进行AP聚类算法。
---


前言：scikit-learn简称sklearn, 机器学习领域当中知名的python模块之一.scikit-learn作为机器学习常用模块，封装了许多机器学习算法，应用起来比较方便，同时自带许多数据集，可以用来测试运行。

## 使用要求

   scikit-learn运行环境要求numpy+scipy+sklearn+matplotlib,配置好pip后在CMD中输入 pip install XXX,即可导入模块，pip等python配置可以参考我之前的文章[python入门及数据基本处理](http://Yangtian.github.io/blog/2018/04/10/bs-data-processing/)

   这里介绍一下导入的几个模块，其实sklearn是一个机器学习算法库，并没有其他模块是必须的，只是可能在算法的使用中涉及到该模块，所以需要导入。

   -  numpy 提供了python对多维数组对象的支持。numpy支持高级大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。

   -  scipy 主要进行数值计算，同时支持矩阵运算，并提供了很多高等数据处理功能，比如积分，傅里叶变换，微分方程求解等。

   -  matplotlib 作图模块，解决可视化问题
   
## sklearn中的AP算法

   **1.函数**

         sklearn.cluster.AffinityPropagation

   **2.主要参数**

         damping : 阻尼系数，取值[0.5,1)

         convergence_iter ：比较多少次聚类中心不变之后停止迭代，默认15

         max_iter ：最大迭代次数

         preference :参考度

   **3.主要属性**

        cluster_centers_indices_ : 存放聚类中心的数组

        labels_ :存放每个点的分类的数组

        n_iter_ : 迭代次数

   **4.示例**     

        preference(即p值)取不同值时的聚类中心的数目在代码中注明了。

## sklearn官网代码 

```python

from sklearn.cluster import AffinityPropagation
from sklearn import metrics
from sklearn.datasets.samples_generator import make_blobs
import numpy as np


## 生成的测试数据的中心点
centers = [[1, 1], [-1, -1], [1, -1]]
##生成数据
Xn, labels_true = make_blobs(n_samples=150, centers=centers, cluster_std=0.5,
                            random_state=0)



simi = []
for m in Xn:
    ##每个数字与所有数字的相似度列表，即矩阵中的一行
    temp = []
    for n in Xn:
         ##采用负的欧式距离计算相似度
        s =-np.sqrt((m[0]-n[0])**2 + (m[1]-n[1])**2)
        temp.append(s)
    simi.append(temp)

p=-50   ##3个中心
#p = np.min(simi)  ##9个中心，
#p = np.median(simi)  ##13个中心    

ap = AffinityPropagation(damping=0.5,max_iter=500,convergence_iter=30,
                         preference=p).fit(Xn)
cluster_centers_indices = ap.cluster_centers_indices_

for idx in cluster_centers_indices:
    print(Xn[idx])

```

这是使用官网的demo的例子，同时我们可以使用自己在解决问题当中使用的数据集，处理成对应的格式。

## 自己的代码
 
程序的目标是，读入一个二维矩阵，代表121个点即类似于官网demo的Xn,之后按照问题处理内容，更新相似度矩阵计算公式，之后设置参考度输出即可。
（PS：我这里处理问题类似于，有121个点有一维的特征，一维特征在data表中读入，之后按照特征进行聚集，特征相似的会被聚集到同一类别，输出聚类中心点，特征数据我是从dataframe结构中导出的，会有一些脏数据，代码中同时包括一些脏数据的处理）

```python
# -*- coding: utf-8 -*-
"""
    作者:     田洋
    日期:     2018/04/18
"""
import pandas as pd
import numpy as np
from sklearn.cluster import AffinityPropagation

import sys

data=pd.read_table('data.txt',sep=',',names=["Speed"])

data1=data.fillna(0)#Nan值的自动填充

l=data1["Speed"].tolist()

dataSet = []
fileIn = open('jvzhen.txt')
for line in fileIn.readlines():
    lineArr = line.strip().split(' ')
    dataSet.append([float(lineArr[0]), float(lineArr[1])])
simi = []
for m in l:
    ##每个数字与所有数字的相似度列表，即矩阵中的一行
    for n in l:
         ##采用速度之差计算相似度
        s =-abs(m-n)
        simi.append(s)
p=np.min(simi)
ap = AffinityPropagation(damping=0.5,max_iter=500,convergence_iter=30,
                         preference=p).fit(dataSet)
cluster_centers_indices = ap.cluster_centers_indices_

for idx in cluster_centers_indices:
    print(dataSet[idx])
```

[数据集data.txt](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/_posts/data.txt)<br>
[数据集jvzhen.txt](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/_posts/jvzhen.txt)


    
