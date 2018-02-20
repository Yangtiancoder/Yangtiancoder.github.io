---
layout:     post
title: 机器学习之策略与模型评估与选择
category: blog
description: 机器学习应掌握的基础知识。
---




由于这部分总记不住，所以摘下来贴在博客上



首先需要明确的是，统计机器学习包括三个部分：1.模型；2.策略；3.算法

其中模型表示的是所要学习的条件概率分布或者决策函数，模型的假设空间包含所有可能的决策函数。我们的目的就是从模型的假设空间中选择最优的一个作为我们的决策函数。那么怎么选择呢，用什么评价标准来选择呢，这就涉及到了第二个要素策略，策略就是我们依照什么样的规则来从假设空间中选择最优的一个决策函数。有了选择模型的方法也即有了这个策略了，接下来的问题就是如何执行这个策略了，这就是第三个要素算法。



一.损失函数、期望风险、经验风险与结构风险


![简陋的草图](http://img.blog.csdn.net/20180111111613888?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVpaDM0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


要区分这三个概念，首先要引入一个损失函数的概念。损失函数是期望风险、经验风险和结构风险的基础。

损失函数是针对单个具体的样本而言的。表示的是模型预测的值与样本真实值之间的差距。比如对于某个样本<Xi,Yi>，其真实的值为Yi,而我们的模型选择决策函数为f,那么通过模型预测的值为f(Xi);损失函数就是用来表示Yi与f(Xi)之间的差距的，我们用函数L(f(Xi),Yi)来衡量。我们希望的是这个L函数最小化。理想的情况是我们的模型决策函数预测值f（Xi）刚好等于该样本的真值Yi。常见的损失函数有以下几种：

 ![简陋的草图](http://img.blog.csdn.net/20150323191253382?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl5YWp1YW41MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

现在我们已经清楚了，对于具体的某个样本点，有了衡量其预测值与真实值的差异度的方法了（选取上面任意一个损失函数即可）。 

通过损失函数我们只能知道模型决策函数f(X)对于单个样本点的预测能力（借用损失函数L(Y,f(x))，损失函数越小，说明模型对于该样本预测越准确。），那么如果想知道模型f(X)对训练样本中所有的样本的预测能力应该怎么办呢？显然只需所有的样本点都求一次损失函数然后进行累加就好了。如下式

![简陋的草图](http://img.blog.csdn.net/20150323191249608?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl5YWp1YW41MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



这就经验风险，所谓的经验风险最小化便是让这个式子最小化，注意这个式子中累加和的上标N表示的是训练样例集中样本的数目。 

经验风险是对训练集中的所有样本点损失函数的平均最小化。经验风险越小说明模型f(X)对训练集的拟合程度越好，但是对于未知的样本效果怎么样呢？我们知道未知的样本数据（<X,Y>）的数量是不容易确定的，所以就没有办法用所有样本损失函数的平均值的最小化这个方法，那么怎么来衡量这个模型对所有的样本（包含未知的样本和已知的训练样本）预测能力呢？熟悉概率论的很容易就想到了用期望。即假设X和Y服从联合分布P(X,Y).那么期望风险就可以表示为：


![简陋的草图](http://img.blog.csdn.net/20150323191336001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl5YWp1YW41MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


这就是期望风险，期望风险表示的是全局的概念，表示的是决策函数对所有的样本<X,Y>预测能力的大小，而经验风险则是局部的概念，仅仅表示决策函数对训练数据集里样本的预测能力。理想的模型（决策）函数应该是让所有的样本的损失函数最小的（也即期望风险最小化），但是期望风险函数往往是不可得到的，即上式中，X与Y的联合分布函数不容易得到。现在我们已经清楚了期望风险是全局的，理想情况下应该是让期望风险最小化，但是呢，期望风险函数又不是那么容易得到的。怎么办呢？那就用局部最优的代替全局最优这个思想吧。这就是经验风险最小化的理论基础。



通过上面的分析可以知道，经验风险与期望风险之间的联系与区别。现在在总结一下：

经验风险是局部的，基于训练集所有样本点损失函数最小化的。

期望风险是全局的，是基于所有样本点的损失函数最小化的。

经验风险函数是现实的，可求的；

期望风险函数是理想化的，不可求的；



只考虑经验风险的话，会出现过拟合的现象，过拟合的极端情况便是模型f(x)对训练集中所有的样本点都有最好的预测能力，但是对于非训练集中的样本数据，模型的预测能力非常不好。怎么办呢？这个时候就引出了结构风险。结构风险是对经验风险和期望风险的折中。在经验风险函数后面加一个正则化项（惩罚项）便是结构风险了。如下式：


![简陋的草图](http://img.blog.csdn.net/20150323191320154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl5YWp1YW41MjE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


相比于经验风险，结构风险多了一个惩罚项，其中是一个lamada是一个大于0的系数。J(f)表示的是是模型f的复杂度。结构风险可以这么理解：

经验风险越小，模型决策函数越复杂，其包含的参数越多，当经验风险函数小到一定程度就出现了过拟合现象。也可以理解为模型决策函数的复杂程度是过拟合的必要条件，那么我们要想防止过拟合现象的方式，就要破坏这个必要条件，即降低决策函数的复杂度。也即，让惩罚项J(f)最小化，现在出现两个需要最小化的函数了。我们需要同时保证经验风险函数和模型决策函数的复杂度都达到最小化，一个简单的办法把两个式子融合成一个式子得到结构风险函数然后对这个结构风险函数进行最小化。



二.误差


![简陋的草图](http://img.blog.csdn.net/20180111112358393?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVpaDM0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)




![简陋的草图](http://img.blog.csdn.net/20180111112413590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVpaDM0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


![简陋的草图](http://img.blog.csdn.net/20180111112426837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveXVpaDM0NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


参考：《统计学习方法》-李航

参考：http://blog.csdn.net/liyajuan521/article/details/44565269
