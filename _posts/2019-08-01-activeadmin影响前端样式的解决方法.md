---
layout: post
title: activeadmin影响前端样式的解决方法
tags:
- Ruby
- ActiveAdmin
categories:
- 技术
excerpt: activeadmin的默认样式会影响用户前端页面

---

ActiveAdmin是一个非常好用的web后台管理gem，功能丰富，灵活多变。

但是，它的样式默认会影响前端页面。

这个问题在每个用activeadmin的新项目中都会遇到，所以记录下来供后面参考。

stackoverflow上面提供了一个[方法](https://github.com/activeadmin/activeadmin/issues/3819#issuecomment-351377822)


1. 首先在vendor/目录下创建assets目录
2. 在vendor/assets目录下创建两个子目录, stytlesheets和javascripts
3. 把assets/stylesheets/active_admin.css 移动到vendor/assets/stylesheets/active_admin.css
4. 把assets/javascripts/active_admin.js.coffee移动到vendor/assets/javascripts目录下面去
5. 重启rails server, 问题解决.


