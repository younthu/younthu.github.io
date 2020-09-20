---
layout: post
title: rails routes中resource和resources的区别
date: 2020-09-13 23:39 +0800
category: rails
tags:
- rails
- routes
- resource
- resources
excerpt: resource 和 resources的区别是resource生成的路径是不带id的。永远不带id，member下面也不带id。
---

rails routes中resource和resources区别是什么?

区别是, resource生成的路径永远不带id, 也没有index路径. 即使嵌套`memmber do`,路径里面也不会有`/:id/`这部分值。

resources生成的路径带id, 还会默认生成 index.  `resources :classes do`下面的路径默认会添加`/:class_id/`.
可以在resources下面嵌套resources， 这样就可以在`rails routes`里面清楚得看到嵌套的id代办的是啥. `/:resource1_id/resource2/:resource2_id/`

resources下面`member do`下面的路径默认添加`/:id/`做为路径的一部分。