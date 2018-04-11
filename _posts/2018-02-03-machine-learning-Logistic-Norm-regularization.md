---
layout:     post
title: 机器学习中的范数规则化
category: blog
description: 机器学习笔记系列。
---





   这篇文章参考自作者zouxy09，做了一些删减适于新手的阅读，也加上一些自己的理解，原作者写的十分专业，比较经典，有不懂得可以参看原文http://blog.csdn.net/zouxy09/article/details/24971995

     

   监督学习的过程可以概括为：最小化误差的同时规则化参数。最小化误差是为了让模型拟合训练数据，规则化参数是为了防止过拟合。参数过多会导致模型复杂度上升，产生过拟合，即训练误差很小，但测试误差很大，这和监督学习的目标是相违背的。所以需要采取措施，保证模型尽量简单的基础上，最小化训练误差，使模型具有更好的泛化能力（即测试误差也很小）。

    

范数规则化有两个作用：

1）保证模型尽可能的简单，避免过拟合。

2）约束模型特性，加入一些先验知识，例如稀疏、低秩​等。


   一般来说，监督学习可以看做最小化下面的目标函数：

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/1.png?raw=true)

   其中，第一项L(yi,f(xi;w)) 衡量我们的模型（分类或者回归）对第i个样本的预测值f(xi;w)和真实的标签yi之前的误差。因为我们的模型是要拟合我们的训练样本的嘛，所以我们要求这一项最小，也就是要求我们的模型尽量的拟合我们的训练数据。但正如上面说言，我们不仅要保证训练误差最小，我们更希望我们的模型测试误差小，所以我们需要加上第二项，也就是对参数w的规则化函数Ω(w)去约束我们的模型尽量的简单。

   OK，到这里，如果你在机器学习浴血奋战多年，你会发现，哎哟哟，机器学习的大部分带参模型都和这个不但形似，而且神似。是的，其实大部分无非就是变换这两项而已。对于第一项Loss函数，如果是Square loss，那就是最小二乘了；如果是Hinge Loss，那就是著名的SVM了；如果是exp-Loss，那就是牛逼的 Boosting了；如果是log-Loss，那就是Logistic Regression了；还有等等。不同的loss函数，具有不同的拟合特性，这个也得就具体问题具体分析的。但这里，我们先不究loss函数的问题，我们把目光转向“规则项Ω(w)”。

   规则化函数Ω(w)也有很多种选择，一般是模型复杂度的单调递增函数，模型越复杂，规则化值就越大。比如，规则化项可以是模型参数向量的范数。然而，不同的选择对参数w的约束不同，取得的效果也不同，但我们在论文中常见的都聚集在：零范数、一范数、二范数、迹范数、Frobenius范数和核范数等等。这么多范数，到底它们表达啥意思？具有啥能力？什么时候才能用？什么时候需要用呢？不急不急，下面我们挑几个常见的娓娓道来。

 
一、L0范数与L1范数

   L0范数是指向量中非0的元素的个数。如果我们用L0范数来规则化一个参数矩阵W的话，就是希望W的大部分元素都是0。这太直观了，太露骨了吧，换句话说，让参数W是稀疏的。OK，看到了“稀疏”二字，大家都应该从当下风风火火的“压缩感知”和“稀疏编码”中醒悟过来，原来用的漫山遍野的“稀疏”就是通过这玩意来实现的。但你又开始怀疑了，是这样吗？看到的papers世界中，稀疏不是都通过L1范数来实现吗？几乎是抬头不见低头见。没错，这就是这节的题目把L0和L1放在一起的原因，因为他们有着某种不寻常的关系。那我们再来看看L1范数是什么？它为什么可以实现稀疏？为什么大家都用L1范数去实现稀疏，而不是L0范数呢？

   L1范数是指向量中各个元素绝对值之和，也有个美称叫“稀疏规则算子”（Lasso regularization）。现在我们来分析下这个价值一个亿的问题：为什么L1范数会使权值稀疏？有人可能会这样给你回答“它是L0范数的最优凸近似”。实际上，还存在一个更美的回答：任何的规则化算子，如果他在Wi=0的地方不可微，并且可以分解为一个“求和”的形式，那么这个规则化算子就可以实现稀疏。这说是这么说，W的L1范数是绝对值，但这还是不够直观。这里因为我们需要和L2范数进行对比分析。所以关于L1范数的直观理解，请待会看看第二节。

   对了，上面还有一个问题：既然L0可以实现稀疏，为什么不用L0，而要用L1呢？个人理解一是因为L0范数很难优化求解（NP难问题），二是L1范数是L0范数的最优凸近似，而且它比L0范数要容易优化求解。所以大家才把目光和万千宠爱转于L1范数。

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/2.png?raw=true)

  OK，来个一句话总结：L1范数和L0范数可以实现稀疏，L1因具有比L0更好的优化求解特性而被广泛应用。


L0和L1都可以实现稀疏化，不过一般选用L1而不用L0，原因包括：1）L0范数很难优化求解（NP难）；2）L1是L0的最优凸近似，比L0更容易优化求解。（这一段解释过于数学化，姑且当做结论记住）

稀疏化的好处是是什么？

1）特征选择

​实现特征的自动选择，去除无用特征。稀疏化可以去掉这些无用特征，将特征对应的权重置为零。

2）可解释性（interpretability）​

例如判断某种病的患病率时，最初有1000个特征，建模后参数经过稀疏化，最终只有5个特征的参数是非零的，那么就可以说影响患病率的主要就是这5个特征。

     

二、L2范数

   除了L1范数，还有一种更受宠幸的规则化范数是L2范数: |它也不逊于L1范数，它有两个美称，在回归里面，有人把有它的回归叫“岭回归”（Ridge Regression），有人也叫它“权值衰减weight decay”。这用的很多吧，因为它的强大功效是改善机器学习里面一个非常重要的问题：过拟合。至于过拟合是什么，上面也解释了，就是模型训练时候的误差很小，但在测试的时候误差很大，也就是我们的模型复杂到可以拟合到我们的所有训练样本了，但在实际预测新的样本的时候，糟糕的一塌糊涂。通俗的讲就是应试能力很强，实际应用能力很差。擅长背诵知识，却不懂得灵活利用知识。例如下图所示（来自Ng的course）：
![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/3.png?raw=true)


   上面的图是线性回归，下面的图是Logistic回归，也可以说是分类的情况。从左到右分别是欠拟合（underfitting，也称High-bias）、合适的拟合和过拟合（overfitting，也称High variance）三种情况。可以看到，如果模型复杂（可以拟合任意的复杂函数），它可以让我们的模型拟合所有的数据点，也就是基本上没有误差。对于回归来说，就是我们的函数曲线通过了所有的数据点，如上图右。对分类来说，就是我们的函数曲线要把所有的数据点都分类正确，如下图右。这两种情况很明显过拟合了。

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/4.png?raw=true)

   OK，那现在到我们非常关键的问题了，为什么L2范数可以防止过拟合？回答这个问题之前，我们得先看看L2范数是个什么东西。

   L2范数是指向量各元素的平方和然后求平方根。我们让L2范数的规则项最小，可以使得W的每个元素都很小，都接近于0，但与L1范数不同，它不会让它等于0，而是接近于0，这里是有很大的区别的哦。而越小的参数说明模型越简单，越简单的模型则越不容易产生过拟合现象。为什么越小的参数说明模型越简单？我也不懂，我的理解是：限制了参数很小，实际上就限制了多项式某些分量的影响很小（看上面线性回归的模型的那个拟合的图），这样就相当于减少参数个数。其实我也不太懂，希望大家可以指点下。

   这里也一句话总结下：通过L2范数，我们可以实现了对模型空间的限制，从而在一定程度上避免了过拟合。

L2范数的好处是什么呢？这里也扯上两点：

1）学习理论的角度：

   从学习理论的角度来说，L2范数可以防止过拟合，提升模型的泛化能力。

2）优化计算的角度：

   从优化或者数值计算的角度来说，L2范数有助于处理 condition number不好的情况下矩阵求逆很困难的问题。

   实际上，在梯度下降中，目标函数收敛速率的上界实际上是和矩阵XTX的 condition number有关，XTX的 condition number 越小，上界就越小，也就是收敛速度会越快。

这一个优化说了那么多的东西。还是来个一句话总结吧：L2范数不但可以防止过拟合，还可以让我们的优化求解变得稳定和快速。

   好了，这里兑现上面的承诺，来直观的聊聊L1和L2的差别，为什么一个让绝对值最小，一个让平方最小，会有那么大的差别呢？我看到的有两种几何上直观的解析：

1）下降速度：

   我们知道，L1和L2都是规则化的方式，我们将权值参数以L1或者L2的方式放到代价函数里面去。然后模型就会尝试去最小化这些权值参数。而这个最小化就像一个下坡的过程，L1和L2的差别就在于这个“坡”不同，如下图：L1就是按绝对值函数的“坡”下降的，而L2是按二次函数的“坡”下降。所以实际上在0附近，L1的下降速度比L2的下降速度要快。所以会非常快得降到0。不过我觉得这里解释的不太中肯，当然了也不知道是不是自己理解的问题。

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/5.png?raw=true)

   L1在江湖上人称Lasso，L2人称Ridge。不过这两个名字还挺让人迷糊的，看上面的图片，Lasso的图看起来就像ridge，而ridge的图看起来就像lasso。

2）模型空间的限制：

   为了便于可视化，我们考虑两维的情况，在(w1, w2)平面上可以画出目标函数的等高线，而约束条件则成为平面上半径为C的一个 norm ball 。等高线与 norm ball 首次相交的地方就是最优解：
![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/images/6.png?raw=true)


   可以看到，L1-ball 与L2-ball 的不同就在于L1在和每个坐标轴相交的地方都有“角”出现，而目标函数的测地线除非位置摆得非常好，大部分时候都会在角的地方相交。注意到在角的位置就会产生稀疏性，例如图中的相交点就有w1=0，而更高维的时候（想象一下三维的L1-ball 是什么样的？）除了角点以外，还有很多边的轮廓也是既有很大的概率成为第一次相交的地方，又会产生稀疏性。

   相比之下，L2-ball 就没有这样的性质，因为没有角，所以第一次相交的地方出现在具有稀疏性的位置的概率就变得非常小了。这就从直观上来解释了为什么L1-regularization 能产生稀疏性，而L2-regularization 不行的原因了。

   因此，一句话总结就是：L1会趋向于产生少量的特征，而其他的特征都是0，而L2会选择更多的特征，这些特征都会接近于0。Lasso在特征选择时候非常有用，而Ridge就只是一种规则化而已。



原文链接：http://blog.csdn.net/zouxy09







