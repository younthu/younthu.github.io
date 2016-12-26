---
layout: post
title: Rails Engine资源加载冲突的问题
tags:
- Rails
- Rails Engine
categories:
- 技术
- Ruby on Rails
excerpt: 在Rails中mount了spree, 同时自定义了spree中的一个页面，通过路径override实现的。该页面用了react_rails], react_rails包加在外层的项目里面，而自定义页面会被spree Engine加载解析。问题来了，它不加载reactjs资源，也不渲染react component，也不抛错! 怎样向Engine注入对react_rails的依赖，加载Engine之外的一些资源呢？
---

# 问题
在Rails中mount了spree, 同时自定义了spree中的一个页面，通过路径override实现的。该页面用了[react_rails](https://github.com/reactjs/react-rails), react_rails包加在外层的项目里面，而自定义页面会被spree Engine加载解析。

问题来了，它不加载reactjs资源，也不渲染react component，也不抛错！

怎样向Engine注入对react_rails的依赖，加载Engine之外的一些资源呢？

# 解决方法
<TBD> 还未找到解决方法。
