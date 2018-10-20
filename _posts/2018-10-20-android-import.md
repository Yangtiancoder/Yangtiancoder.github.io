---
layout: post
title: 解决Cannot resolve symbol XXX
category: blog
tags: [Android，Java]
description: AS导入遇到的问题。
---


## Cannot resolve symbol XXX  

**第一种情况包没有导入的问题**  

解决办法

1.在build.gradle 中加入implementation 'com.android.support:appcompat-v7:27.0.1'

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/AS-import.png?raw=true)

2.利用Android Studo 导入包

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/AS-import1.png?raw=true)


**第二种情况**  

V7包我在SDK中已经下载了，新导入的项目不知道为什么无法解析，出现Cannot resolve symbol AppCompatActivity遇到过两次，第一次跟着经验乱搞，第二回又遇到忘记第一回怎么弄的了。

解决办法：

1.删除.idea文件，据说包含一些原来的配置信息等

2.Invalidate Caches/restart

![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/AS-import2.png?raw=true)
