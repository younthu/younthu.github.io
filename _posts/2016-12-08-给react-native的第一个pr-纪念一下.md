---
layout: post
title: 给React-Native的第一个PR，纪念一下
---

#给Facebook的第一个PR被接受了
今天早上给[Facebook/react-native](https://github.com/facebook/react-native)的第一个[PR](https://github.com/facebook/react-native/pull/11298)被合并进去了，commit在[这里](https://github.com/facebook/react-native/commit/dbcfc2b41f03867108913044c263cf90e3953db1). 

#关于这个PR
这个PR缘起于一个React Native 里面 [HTTP2的坑](https://github.com/facebook/react-native/issues/11283), 该问题解决方案见本博客另外一个文章[2016-11-30-okhttp中的坑]. 

在编译私有React-Native代码的时候发现boost下载总失败，抛403错误，最后发现升级gradle-download-task以后就能下载。遂向react-native提交了一个文档更新PR.
