---
layout: post
title: python入门及数据基本操作
category: blog
tags: [python,毕设]
description: 毕设涉及到的学习内容。
---



前言：据说python也即将列入中学学习内容，意味着高中生不仅coding可能即将开始大面积搞AI了,全面被吊打被预警。。。企业招聘基本开发岗几乎也是基础C系列或java一种，加上脚本语言语言python之类的，至于python应用多么广泛就不说了，python发展感觉还是比较具有潜力的，由于具有代码简单易读，移植性好，维护成本低等等优点，深受工业界和学术界的喜爱，总之还是得好好搞起来。


##配置

  **1.环境安装配置**

  1.python现在安装配置比较简单，访问<http://www.python.org/download/>去下载最新的python版本。我用的是python3，一路next过来即可，还有python2的版本，如果想要兼容这两个版本，可以装一个conda类似于一个包管理器，可以制定python环境，我倾向于现在还是学3吧，因为各种版本库也逐渐加强对于3的支持，2与3也稍微有些语法的不同，但是也差不多，思想都是类似的，学哪个都可以。


  2.下载完成后，计算机添加安装目录到环境变量，把python的安装目录添加到pth系统变量中即可。


  3.测试python安装是否成功，cmd打开命令行输入 python 命令，如下图即成功：

  ![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/p2.png?raw=true)

  **2.工具**

  现在电脑已经具有python的环境，现在本地运行基本可以用来测试或者写代码都可以，再用一款文本编辑器，类似于Sublime或者Notepad++基本可以完成日常的开发，都是免费的，推荐的话我建议用Sublime，因为比较好看。。。有了文本编辑器后，可以随心所欲的coding（要符合python的语法,和你配置的环境统一），然后送给python解释器去执行，主要方法有两种都可以
  方法一： 

  进入Python脚本所在的文件夹，shift+右击，选择“在此处打开命令窗口”，按TAB键切换文件，选择目标python脚本，当然直接输入也行。
  方法二： 

  win+R，输入cmd，进入命令行窗口，直接将脚本文件拖到窗口里回车就可以了。
  
  这个主要和前面测试代码的方法分开就好了，一个类似于交互式的python解释器环境可以用于测试等等，然后将结果输出，另一种行为是将"XX.py"当做参数传递给解释器，触发了解释器的另一个行为：从文件中读取Python语句并执行。

  **3.管理**
  
  类似于java web中的包管理工具maven，python也有类似于管理各种库的东西，python主要就强大在各种库近几天接触向numpy和pandas等等，同样他也有许多内置的库，其实在python中称为库并不准确，都是叫各种的模块，类似库的概念。用到的工具就是pip，实现类似maven的功能，这个也比较简单，不需要复杂的配置，详见<http://www.ttlsa.com/python/how-to-install-and-use-pip-ttlsa/>

  **4.IDE**

  库也有了，环境也有了，进行大型项目开发还少不了IDE，推荐使用pycharm，风格类似于Intellij，用起来也比较舒服，同样有免费版,配置起来也不复杂。-[安装教程戳](https://blog.csdn.net/qq_29883591/article/details/52664478)


  之后可以进行愉快的开发了，我碰到的问题有类似于库关联的问题，需要在IDE选项中注意关联自己的环境和添加上pip导入的包，具体步骤Settings -->Project:Python -->Project Interpreter，如图（注意+号那里添加库）

  ![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/p3.png?raw=true)


##数据采集基本操作

   
  常用的数据分析文件格式有txt,csv（主要）,json等等。

  
  **txt基本操作**
  打开文件
   
   ```python
    file_obj = open(filename, access_mode)
    access_mode: ‘r’，‘w’
   ```
   读操作
   ```python
    file_obj.read() #读取整个文件内容
    file_obj.readline() #逐行读取
    file_obj.readlines() #返回列表，列表中的每个元素是行内容

   ```

   写操作
   ```python
    file_obj.write()# 将内容写入文件
    file_obj.writelines() #将字符串列表内容逐行写入文件

   ```

  **csv基本操作**

   常用csv文件一般用pandas处理。
   读操作
   ```python
   df_obj = pd.read_csv()#返回DataFrame类型的数据

   ```

   写操作
   ```python
    fdf_obj.to_csv()
   ```
   pandas介绍：

   Python Data Analysis Library 或 pandas 是基于NumPy 的一种工具，该工具是为了解决数据分析任务而创建的。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。pandas提供了大量能使我们快速便捷地处理数据的函数和方法。你很快就会发现，它是使Python成为强大而高效的数据分析环境的重要因素之一。

   数据结构：

   Series，类似于一维数组的对象。
   DataFrame，表格型数据结构，每列可以是不同的数据类型，可表示二维或更高维的数据


##练习代码

 ```python
    
  # -*- coding: utf-8 -*-

  """
    作者:田洋
    日期:2018/04/03
    功能：利用Dataframe结构完成类似于SQL功能的查询和筛选

  """
  import pandas as pd
  import numpy as np
  import sys
  print("请输入时间段：形如0:12-0:22")
  a=input()
  b=a.split('-')
  c='2011-9-14 '+b[0]+':00'
  d='2011-9-14 '+b[1]+':00'
  data=pd.read_table('GP.txt',sep=',',names=["ID","Time","Longitude","Latitude","Speed","No"])
  data1=data[(data.Time>c)&(data.Time<d)]
  print(data1)
 ```

 [数据集下载](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/_posts/GP.txt)


