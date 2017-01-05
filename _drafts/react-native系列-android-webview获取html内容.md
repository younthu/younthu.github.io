---
layout: post
title: React Native系列 - Android webview获取html内容
categories:
- 技术
- Android

tags:
- Android
- html
- headers

excerpt: Android webview没有办法获取加载完成的页面的response.headers，也没有简单办法获取加载完成后的网页内容。android这样设计的原因网上的解释是说处于安全的考虑，防止别人篡改网页内容（但是注入javascript又可以改内容，so，这个设计显得有点多余）。所以，想要获取response.headers, 除了自己发http request, 没有办法，对于想要走网页完成第三方验证登录然后再获取response.headers的同学这可就悲催了。要获取webview里面返回的内容则需要废点周折。
---

# 先上结论

1. android 下面没法获取webview加载完成后的response.headers, 通过java代码做不到，通过在页面注入javascript也做不到。
1. 获取webview的html content不能通过java直接获取，要通过注入javascript获取。

# 怎样在React Native 里面获取webview 的body。
