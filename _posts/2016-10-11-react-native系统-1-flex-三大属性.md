---
layout: post
title: React Native系列(1) - Flex 三大属性
tags:
- React Native
---
## Flex
React Native 用了Flexbox布局机制, 是一种自适应的弹性布局。用过Bootstrap的应该很容易上手这个东西。
如果你没有了解过Bootstrap的布局也没有关系，只要搞懂Flex的三大属性，基本上能轻松搞定大部分样式。

## Flex三大属性(flexDirection, justifyContent, alignItems)

如果我们用flex布局，有三大属性是很容易让人懵逼的(我是狠狠地懵逼了一段时间)。这三大属性分别是:

1. flexDirection（控制元素的生长/主轴方向)
1. justifyContent(控制元素在生长/主轴方向上的水平对齐方式)
1. alignItems(控制元素的生长/主轴方向上的垂直对齐方式)


理解了三个概念就可以开始干活了。

## 用word的文字对齐来理解这三个概念
大家都熟悉用office的word来做文字排版，我们就用word的文字排版来解释这三个个概念。

1. flexDirection对应于文字的排版。是横排还是竖排. 因为阅读习惯，大部分情况下我们都是水平排版的。这个值一般不用管。

	如果给flexDirection赋值'column'（默认），那就是竖排。赋值'row'就是横排。没有其它值。

1. justifyContent用来控制文字的水平对齐方式。是左对齐，还是右对齐，还是居中对齐，全靠这个来控制。复杂点说，它控制的是flexDirection方向上的对齐方式。

1. alignItems用来控制文字垂直方向的对齐方式。顶部对齐，居中对齐还是底部对齐就靠它。复杂点说，它控制的是与flexDirection方向相垂直的方向上的对齐方式。

## 参考
1. [三大属性的讲解和在线代码编辑演示](https://facebook.github.io/react-native/docs/flexbox.html)
1. [Flex更多属性详解](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
