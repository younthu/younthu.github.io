---
layout: post
title: python skimage骰子作画
date: 2021-01-28 09:23 +0800
excerpt: '通过骰子作图，该算法把图片转化成用骰子代表的像素点阵.'
---
前面根据阮一峰的一篇博客[骰子作画](http://www.ruanyifeng.com/blog/2011/11/dice_portrait.html)，写了一个把图片通过骰子作画的工具。
算法是抄的，用python实现，然后加了一些功能。

它的核心思想就是把一张图片划成16乘16的小区域，每个区域取它的灰度值，然后根据灰度值划成六段，编号为一至六，然后将一至六映射到骰子的一到六个点。

于是，一张彩色的图片就可以通过这种方式变成骰子拼成的黑白图片。

这个算法最早来自于一个国外的博主Scott MacDonald，他最早公布了他的算法,在[这个地方](http://www.elusivesnark.com/2008/11/carolines-dice-portrait.html)。

我用python加SK1米即实现了这个算法，算法已经公开在github上面，大家都可以去看, 在[这里](https://github.com/younthu/image2dice.git)。
目前大概3k颗左右的骰子就可以做出一副效果不错的骰子图。

下面是用到的骰子.

![dice](/assets/img/dice/11.jpg) ![dice](/assets/img/dice/22.jpg)
![dice](/assets/img/dice/33.jpg)
![dice](/assets/img/dice/44.jpg)
![dice](/assets/img/dice/55.jpg)
![dice](/assets/img/dice/66.jpg)

下面是原图和骰子作画效果.

![dice](/assets/img/dice/wyz3.jpeg)
![dice](/assets/img/dice/wyz3.gray.dice.pic.png)

后续呢，打算用机器学习做一些优化，主要是针对有方向性的骰子，比如说二号三号六号，根据图像特征做旋转。

这种优化只能是局部优化，对整体影响不大，但细节上应该会提升一些。


一个需要注意的地方是，rgb取灰度值的时候Red, Green, Blue权重不是一样的，是根据人的直观感受做了调整的。
~~~python
def rgb2gray( argb):
    return int(0.3*argb[0] + 0.59*argb[1] + 0.11*argb[2])
~~~