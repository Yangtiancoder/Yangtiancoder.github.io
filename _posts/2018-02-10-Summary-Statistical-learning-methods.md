---
layout: post
title: 《统计学习方法》
category: blog
tags: [机器学习, 总结]
description: 统计学习方法总述。
---


目前为止关于《统计学习方法》-李航，这本书的内容基本上大致有了系统的了解，我个人觉得这本书还是比较不错的，里面还有很多细节啊，等等诸多公式的推导，有一些深的方面同时呢，又有适于我这种很初级入门的基本原理，总体来说还是很适合入门的，至于我个人呢，通读一遍下来，感觉稍微有了一些认识的基础，应该还是要再看第二遍的，里面有一些公式的推导啊，一些术语啊理解还不是很深刻，刚才回看了一些感知机的内容有看到了许多新的东西，知识架构也需要一步一步完善的吧，不过感觉第二遍应该挺快的，其中有几个经常使用的模型，例如SVM，序列标注的HMM和CRF，可能以后在别的项目中也会用到，可能也需要重点再看一下

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/31.png?raw=true)


首先至于适用问题，可以分为回归、分类、标注，问题，而标注又可视为多分类的问题，而回归和分类本质有差不多，只不过分类将回归输出离散化，说白了搞懂分类模型，其他的也八九不离十了吧，至于模型特点大致意思就是可以从我要这个模型来干什么，目的性也就决定了一个模型的特点，比如感知机和SVM都是分离超平面，只不过SVM取最大间隔保持确信度，又加了核技巧来区分非线性模型，其本质和目的都是二类分类，至于模型类别，生成模型就是生成概率密度函数来对输入的随机变量预测，适用于数据量比较大的情况，而判别模型则直接学习决策函数作为预测的模型，数据量比较小的情况下较为准确。


![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/32.png?raw=true)

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/33.png?raw=true)



上面的内容是比较重要的吧，比如正则化项，参数越多模型越复杂，越容易过拟合因此需要正则化项来提高模型的泛化能力，让他具有很好的预测能力，还有不同损失函数用在哪个模型上，例如逻辑斯蒂回归取对数在极大似然估计中便于求导计算等等

![简陋的草图](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/34.png?raw=true)


到这里，就基本结束了，一些新的内容也会继续补充上，也会多多加入一些自己的理解，便于记忆，还是要多多努力希望能坚持下去。

