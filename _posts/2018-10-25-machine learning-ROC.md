---
layout: post
title: 卡方检验、COX和ROC等
category: blog
tags: [Machine Learning]
description: 最近遇到的ML概念。
---

# 对数秩和检验

对数秩和检验是以生存时间的对数为基础推导出来的，其基本思想是实际死亡数和期望死亡数间比较。他对各组生存率做整体比较，故应用范围广。它适用于两组及多组生存率间比较。

用对数秩和检验对样本的生存率进行比较时。，要求各组生存曲线不能交叉，生存曲线的交叉提示存在某种混杂因素，因此应采用分层的办法或者多因素方法来矫正混杂因素。另外当假设检验推断有差别时，可以通过生存曲线，半数生存期及相对危险度等指标来评价其效果。

# Cox模型

处理多因素生存数据的回归模型-比例危险度模型。

Cox模型适用于处理单因素或多因素影响下的时间-反应数据，也就是多因素生存分析的数据。这种数据的主要特点是除了有关因素外，每研究对象的随访记录都有两个变量所组成。一个为观察时间一个为结局是否发生。特别适用于随访迟早不一，随访时间长短不一情况处理。

回归系数 回归系数用来反映因素对生存时间的影响的强度，一般而言，回归系数愈大，则因素对生存时间的影响越大。

# ROC曲线

在介绍ROC曲线之前，先说说混淆矩阵及两个公式，因为这是ROC曲线计算的基础。

-  1.混淆矩阵的例子(是否点击广告)：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/ROC-1.png?raw=true)

说明：

   TP：预测的结果跟实际结果一致，都点击了广告。  

   FP：预测结果点击了，但是真实情况是未点击。  

   FN：预测结果没有点击，但是真实情况是点击了。  

   TN：预测结果没有点击，真实情况也是没有点击。  

-  2.两个公式：  

    1）真正率：  

    TPR=TP/(TP+FN)  

    2）假正率

     FPR=FP/(FP+TN)  
-  3.ROC曲线就是真正率随假正率的变化情况。下面用一段代码展示一下(sklearn包中包含相关算法)：  

```python
##导入相关包
import numpy as np
from sklearn import metrics
import matplotlib.pyplot as plt
##设置y值：表示实际值
y = np.array([1, 1, 2, 2])
##设置pred值：表示预测后的值
pred = np.array([0.1, 0.4, 0.35, 0.8])
##计算相关数据：注意返回的结果顺序
fpr, tpr, thresholds = metrics.roc_curve(y, pred, pos_label=2)
##计算曲线下面积
roc_auc=metrics.auc(fpr, tpr)
##绘图
plt.clf()
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.0])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.legend(loc="lower right")
plt.show()
```

结果如图所示：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/ROC-2.png?raw=true)

-  4.关于ROC曲线

  1）虚线所示直线随机分类时的ROC曲线，一般画到图中作为参照点

  2）对于一个完美的分类器，ROC曲线应该是从(0,0)到(0,1)，然后横着连到(1,1)的折线

  3）ROC曲线越接近左上角，分类效果越好

-  5.关于AUC

  1）AUC表示曲线下面的面积

  2）对于一个完美的分类器，AUC的值应该为1

  3）对于一个随机猜测分类器(即图中虚直线)，AUC的面积为0.5

  4）AUC面积越大，分类效果越好
  
# 卡方检验

## 什么是卡方检验

卡方检验是一种用途很广的计数资料的假设检验方法。它属于非参数检验的范畴，主要是比较两个及两个以上样本率( 构成比）以及两个分类变量的关联性分析。其根本思想就是在于比较理论频数和实际频数的吻合程度或拟合优度问题。

它在分类资料统计推断中的应用包括：两个率或两个构成比比较的卡方检验；多个率或多个构成比比较的卡方检验以及分类资料的相关分析等。

## 举例一：以下为一个典型的四格卡方检验，我们想知道喝牛奶对感冒发病率有没有影响：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/kafang-1.png?raw=true)

通过简单的统计我们得出喝牛奶组和不喝牛奶组的感冒率为30.94%和25.00%，两者的差别可能是抽样误差导致，也有可能是牛奶对感冒率真的有影响。

为了确定真实原因，我们先假设喝牛奶对感冒发病率是没有影响的，即喝牛奶喝感冒时独立无关的，所以我们可以得出感冒的发病率实际是（43+28）/（43+28+96+84）= 28.29%

所以，理论的四格表应该如下表所示：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/kafang-2.png?raw=true)

即下表：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/kafang-3.png?raw=true) 

如果喝牛奶喝感冒真的是独立无关的，那么四格表里的理论值和实际值差别应该会很小。

-  卡方检验的计算公式为：

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/kafang-4.png?raw=true) 

其中，A为实际值，T为理论值。

x2用于衡量实际值与理论值的差异程度（也就是卡方检验的核心思想），包含了以下两个信息：

1. 实际值与理论值偏差的绝对大小（由于平方的存在，差异是被放大的）

2. 差异程度与理论值的相对大小

根据卡方检验公式我们可以得出例1的卡方值为：

卡方 = (43 - 39.3231)平方 / 39.3231 + (28 - 31.6848)平方 / 31.6848 + (96 - 99.6769)平方 / 99.6769 + (84 - 80.3152)平方 / 80.3152 = 1.077

卡方分布的临界值

上一步我们得到了卡方的值，但是如何通过卡方的值来判断喝牛奶和感冒是否真的是独立无关的？也就是说，怎么知道无关性假设是否可靠？

答案是，通过查询卡方分布的临界值表。

这里需要用到一个自由度的概念，自由度等于V = (行数 - 1) * (列数 - 1)，对四格表，自由度V = 1。
对V = 1，喝牛奶和感冒95%概率不相关的卡方分布的临界概率是：3.84。即如果卡方大于3.84，则认为喝牛奶和感冒有95%的概率不相关。

显然1.077<3.84，没有达到卡方分布的临界值，所以喝牛奶和感冒独立不相关的假设不成立。

-  卡方分布表

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/kafang-5.png?raw=true) 

**参考资料**

<https://blog.csdn.net/ludan_xia/article/details/81737669>  
<https://www.cnblogs.com/dlml/p/4403482.html>


