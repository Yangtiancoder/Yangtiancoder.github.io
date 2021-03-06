---
layout: post
title: 机器学习笔记（二）之感知机
category: blog
tags: [Machine Learning]
description: 机器学习笔记系列。
---


在机器学习中，感知机（perceptron）是二分类的线性分类模型，属于监督学习算法。输入为实例的特征向量，输出为实例的类别（取+1和-1）。感知机对应于输入空间中将实例划分为两类的分离超平面。感知机旨在求出该超平面，为求得超平面导入了基于误分类的损失函数，利用梯度下降法对损失函数进行最优化（最优化）。感知机的学习算法具有简单而易于实现的优点，分为原始形式和对偶形式。感知机预测是用学习得到的感知机模型对新的实例进行预测的，因此属于判别模型。感知机由Rosenblatt于1957年提出的，是神经网络和支持向量机的基础。


 原理阐述

先简单的构思下，你觉得感知机是如何工作的？感知机需要几个二进制输入， X1，X2，…Xn ，并产生一个二进制输出：

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/24.JPG?raw=true)


上图所示的  Perceptron 有三个输入，但是实际的输入可能远多于三个或是少与三个。  Rosenblatt 提出了一个简单的规则来计算输出，他首先引入了  weights（权重）概念， ω1，ω2,...。以实数权重  ω表示输入到输出的重要性，神经元的输出 0 或 1 ，这取决于加权因子（即  weights） 这里写图片描述小于或大于某一阈值。就像权重，阈值为一个实数，是一个神经元的参数。


举个简单的例子，假设在这个周末，你听说你的城市将有一个摇滚音乐节，你喜欢摇滚，现在想做决定看是否去音乐节。假设你通过权衡三个因素来做出你的决定：

1）当天的天气好吗？ 
2）你的男朋友或女朋友是否想陪你一起去？ 
3）到达目的地的交通是否便利。

下面我们把它公式化，我们以相应的二进制变量来表示这三个因素  x1,x2,x3 。假设我们把天气好表示为 x1=1 ，反之 x1=0 。相似地假设，有人陪 x2=1 , 反之  x2=0; 交通便利 x3=1 ,反之  x3=0。

现在，假设： 
1、你非常喜欢摇滚音乐，所以即使的男、女朋友不去，你也很可能会去参加这个摇滚音乐节。 
2、但也许你真的讨厌的坏天气，如果初先坏天气，你很可能就不去了。

你可以使用感知器模型来做这类决策，这时我们设置权重因子 w1=6,w2=2,w3=2. 对 w1值越大，表明天气因素对你有更重要的意义，远不如是否有人陪伴或是交通情况。最后，假设你选择一个 阈值为 5 的感知器。有了这些选择，感知机可实现你理想的决策模型，当天气好的时候，输出 1； 0 表示天气不好。这时，无论你的男朋友或女朋友是否想去，或是交通情况是否便利，这两项将不再影响你的输出。

通过不同的权重和阈值，我们可以得到不同的决策模型。例如，假设我们选择了一个阈值为 3。然后，如果天气好，感知器 会判定你应该去参加这个音乐节。亦或是天气不好，但是你的男、女朋友愿意陪你去并且交通便利，感知器 也会判定定你应该去参加这个音乐节。总而言之，权值或是阈值的改变，它将会是一个不同的决策模型。

显然，感知器不是人类的一个完整的决策模型,人的神经网络要复杂得多！但是，例子说明的是一个感知器是如何衡量不同种类的约束条件的重要性，以作出决定。就这方面而言，举例还算是恰当。当然，一个复杂的网络感知器就可以做出非常微妙的决定：



定义

假设输入空间(特征向量)为X⊆Rn，输出空间为Y={-1, +1}。输入x∈X表示实例的特征向量，对应于输入空间的点；输出y∈Y表示示例的类别。由输入空间到输出空间的函数为


f(x)=sign(w⋅x+b)
称为感知机。其中，参数w叫做权值向量weight，b称为偏置bias。w⋅x表示w和x的点积


∑i=1mwixi=w1x1+w2x2+...+wnxn
sign为符号函数，即


f(x)={+1−1if x>=0else


感知机的几何解释：



可以把感知机看成n维实例空间的决策超平面：W*X=0.这个超平面将空间中的点分为正负两类。如果给定的杨莉集线性可分，则可以将每个样例准确地分为两类，称为分类超平面。而其中（w1,w2，...,wn）为这个超平面的法向量，w0称为截距。


在二分类问题中，f(x)的值（+1或-1）用于分类x为正样本（+1）还是负样本（-1）。感知机是一种线性分类模型，属于判别模型。我们需要做的就是找到一个最佳的满足w⋅x+b=0的w和b值，即分离超平面（separating hyperplane）。如下图，一个线性可分的感知机模型


![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/25.JPG?raw=true)
中间的直线即w⋅x+b=0这条直线。

线性分类器的几何表示有：直线、平面、超平面。




学习策略
核心：极小化损失函数。

如果训练集是可分的，感知机的学习目的是求得一个能将训练集正实例点和负实例点完全分开的分离超平面。为了找到这样一个平面（或超平面），即确定感知机模型参数w和b，我们采用的是损失函数，同时并将损失函数极小化。

对于损失函数的选择，我们采用的是误分类点到超平面的距离（可以自己推算一下，这里采用的是几何间距，就是点到直线的距离）

对于误分类点(xi,yi)来说：


−yi(w∗xi+b)>0

L(w,b)=−∑xiϵMyi(w∗x0+b)
其中M为误分类的集合。这个损失函数就是感知机学习的经验风险函数。

可以看出，随时函数L(w,b)是非负的。如果没有误分类点，则损失函数的值为0，而且误分类点越少，误分类点距离超平面就越近，损失函数值就越小。同时，损失函数L(w,b)是连续可导函数。

学习算法
感知机学习转变成求解损失函数L(w,b)的最优化问题。最优化的方法是随机梯度下降法（stochastic gradient descent），这里采用的就是该方法。关于梯度下降的详细内容，参考wikipedia Gradient descent。下面给出一个简单的梯度下降的可视化图：


![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/26.JPG?raw=true)

上图就是随机梯度下降法一步一步达到最优值的过程，说明一下，梯度下降其实是局部最优。感知机学习算法本身是误分类驱动的，因此我们采用随机梯度下降法。首先，任选一个超平面w0和b0，然后使用梯度下降法不断地极小化目标函数


minw,bL(w,b)=−∑xiϵMyi(w∗x0+b)
极小化过程不是一次使M中所有误分类点的梯度下降，而是一次随机的选取一个误分类点使其梯度下降。使用的规则为 θ:=θ−α∇θℓ(θ)，其中α是步长，∇θℓ(θ)是梯度。假设误分类点集合M是固定的，那么损失函数L(w,b)的梯度通过偏导计算：


∂L(w,b)∂w=−∑xiϵMyixi

∂L(w,b)∂b=−∑xiϵMyi
然后，随机选取一个误分类点，根据上面的规则，计算新的w,b，然后进行更新：


w:=w+ηyixi

b:=b+ηyi
其中η是步长，大于0小于1，在统计学习中称之为学习率（learning rate）。这样，通过迭代可以期待损失函数L(w,b)不断减小，直至为0.

下面给出一个感知器学习的图，比较形象：


![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/27.JPG?raw=true)
由于上图采取的损失函数不同，所以权值的变化式子有点区别，不过思想都是一样的。



扩展：梯度概念




方向导数？
在微积分课程中，我们知道函数在某一点的导数（微商）代表了函数在该点的变化率。微分和积分，它们的定义都是建立在极限的基础上。对于单变量函数f(x)，它在x0处导数是：当x趋近于x0时，函数的改变量与自变量的改变量的比值的极限，即微商（导数）等于差商的极限﻿


f′(x0)=limΔx→0f(x0+Δx)−f(x0)Δx
对于单变量函数，自变量只有一个，当x趋近于x0时只能在直线上变动，移动的方向只有左右两方。
然而，对于多变量函数，自变量有多个，表示自变量的点在一个区域内变动，不仅可以移动距离，而且可以按任意的方向来移动同一段距离。因此，函数的变化不仅与移动的距离有关，而且与移动的方向有关。因此，函数的变化率是与方向有关的。这也才有了方向导数的定义，即某一点在某一趋近方向上的导数值。

多变量函数的极值
对于单变量函数，若在某点取得极值，则该点的导数为0。同样对于多变量函数，在某点为极大值或极小值只有当在该点的每个偏导数等于0才有可能，也就是说梯度等于0。因此，在多变量函数中，驻点，也就是导数为0的点，指的是每个偏导数等于0，也就是梯度等于0的点。进而，在求极值时，我们可以先找到梯度为0的驻点，在通过定理（查书呗）判断它是否是极值点，极大值还是极小值。




易混淆概念：感知机和SVM




1、相同点

都是属于监督学习的一种分类器（决策函数）。

2、不同点

感知机追求最大程度正确划分，最小化错误，效果类似紫线，很容易造成过拟合。
支持向量机追求大致正确分类的同时，一定程度上避免过拟合，效果类似下图中的黑线。
感知机使用的学习策略是梯度下降法，而SVM采用的是由约束条件构造拉格朗日函数，然后求偏导令其为0求得极值点。这里特别说明下一般我们的拉格朗日函数是符合凸函数的，因此对于凸函数一定存在极值点，也是唯一的最优解。而一般的非凸函数，只好采用梯度下降法一步一步的求得极值点，如果非凸函数还是采用求导令为0，可能找不到极值点！因为鞍点也是导数为，但却不是极值点的特例，如y = x^3函数。导数为0是函数极值点的必要条件。


![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/28.JPG?raw=true)


感知机的缺点
感知机是线性的模型，其不能表达复杂的函数，不能出来线性不可分的问题，其连异或问题(XOR）都无法解决，因为异或问题是线性不可分的，怎样解决这个问题呢，通常有两种做法。 

PS:（异或问题http://blog.csdn.net/yuih344/article/details/79025149 ）

其一：用更多的感知机去进行学习，这也就是人工神经网络的由来。 

其二：用非线性模型，核技巧，如SVM进行处理。


参考：统计学习方法-李航

参考：http://blog.csdn.net/dream_angel_z/article/details/48915561

参考：https://www.zhihu.com/question/51500780

参考：http://blog.csdn.net/eddy_zheng/article/details/50704809
