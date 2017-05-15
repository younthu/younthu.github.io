---
layout: post
title: sumy, textsum和fairsqe
categories:
- 技术
tags:
- 机器翻译
- 机器摘要

---
前端时间做了一点点机器摘要和机器翻译的工作，现在记录下来，回头可以参考。

机器摘要用了[sumy](https://github.com/miso-belica/sumy),做了一些简单的二次开发工作。 然后花了一个多星期研究了一下抽相式摘要生成算法[google's textsum algorithm](https://github.com/tensorflow/models/tree/master/textsum)，同时还做了一个支线任务:研究facebook的机器翻译工具[fairseq](https://github.com/facebookresearch/fairseq)。

总体上来讲，机器摘要和人工摘要肯定还是有不小的差距。机器翻译也一样，有些翻译的结果需要人工后期矫正才好用。

从我们项目实践来看，机器摘要和机器翻译还是会省一些人工成本的。

接下来记录一下三件工作的细节和体会。

## [sumy](https://github.com/miso-belica/sumy)
Sumy 是一个比较成熟的摘要算法库，开源免费的。它提供命令行工具，同时也提供python接口. 使用方法比较简单，不详细列举。

从我们使用的效果来看，它自带的算法里面Luhn, LexRank和TextRank这三者效果最好。

Sumy的不足之处在于好像自己想改进其算法的话难度挺大。没有说明文档，没有提供接口做参数调整。它的[github](https://github.com/miso-belica/sumy)页面有提供几份papers的链接. 做为一个天天赶进度的人，你让我读paper？

因为Sumy使用简单，集成方便，所以我们项目最后决定从Sumy入手。

## [Google textsum](https://github.com/tensorflow/models/tree/master/textsum)
这是一个利用了深度学习技术的机器摘要算法。从它的[github](https://github.com/tensorflow/models/tree/master/textsum)首页的描述来看，确实效果挺好的。不过从[另外一篇博客](http://pavel.surmenok.com/2016/10/15/how-to-run-text-summarization-with-tensorflow/)的评论来看，问题也不少。

**怎么跑这个算法?**

跑这个算法比较简单，按照他的页面说明就可以了。有几点需要注意的地方:

1. 作者本身用了一个很专业的语料库[Annotated English Gigaword](https://catalog.ldc.upenn.edu/LDC2012T21)，这个库需要$6000授权费。如果没有语料库授权，你可以用作者提供的toy dataset来跑，记得自己把他提供的data文件重命名为training-**, validation-* 和test-*来分别跑训练，验证和摘要生成。那条decode命令就是用来生成摘要的。
2. [这篇博客](http://pavel.surmenok.com/2016/10/15/how-to-run-text-summarization-with-tensorflow/)有介绍怎么准备CNN语料库和dailymail语料库。虽然比不上Gigaword,但是也比toy data set要大很多了。这两个语料库很难下载，可能因为是一个一个页面爬的，跑了很多遍，总是爬不完整。 有爬完整的同学希望能共享一下。
2. 作者是在多机器多GPU的环境下跑的。我在macbook pro上跑了，完全跑不动。租了一台amazon的gpu instance, 跑了几天，内存不够(只有4G显存)，被killed。接下来打算自己组一台机器来跑。
3. 这个算法跑起来基本上都是按周来计时的。深度学习到底是不是一个烧硬件的陷阱？希望深度学习进化快点，别浪费太多的显卡资源。

这个算法让我有一种深深的担忧: 人类又要陷入GPU军备竞赛了，上次是挖比特币，这次是深度学习。 听说nvidia的股票都翻了好几倍了。微软都开始上FPGA了。

**建议**

这个方向是一个非常好的方向，可能会替代传统的机器摘要算法. 但是当前它的不足也很明显，然后在网上被讨论得也不多。 建议还是先用sumy，把业务模型搭建起来，验证一下市场需求，textsum做为后期改进吧。

## [Fairseq](https://github.com/facebookresearch/fairseq)
[Fairseq](https://github.com/facebookresearch/fairseq)是Facebook开源的机器翻译库，刚开源就获得了一千多stars, 也是基于机器学习的。 机器学习真是火啊！

它基于sequnce to sequence算法, 基于机器学习库[Torch](Torch installation). 也是一个烧GPU的东西。

它提供了一些训练好的模型，可以直接用来做验证。我们验证了一下英语到德语的翻译，效果还不错。可惜没有英语到中文的模型，也找不到英语到中文的翻译语料库。

因为它提供了训练好的模型，所以你要尝试它的话非常简单。下载训练好的模型，然后用`fairseq generate-line` 就可以做翻译了。


**模型的训练**
该库本身基于[WMT](http://statmt.org/wmt14)语料库做了一些训练，可惜WMT主要针对的是欧洲的几种语言，没有英文到中文的翻译。在网上找了一些，也没有找到中英翻译的语料库，放弃训练。

**建议**
我们打算用[google translate api](https://cloud.google.com/translate/docs/). 以后需要自己开发翻译算法的话可以回到这里来。 但是，这个翻译语料好像始终是个大问题啊。



