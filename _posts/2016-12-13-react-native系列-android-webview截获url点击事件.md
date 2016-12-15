---
layout: post
title: React Native系列 - android webview截获url点击事件
---
#React Native Android不支持 onShouldStartLoadWithRequest
React Native iOS里面可以很轻松的截获网页url点击事件，只要响应`onShouldStartLoadWithRequest`就可以了,底层是iOS的shouldStartLoadForRequest。安卓的没有同样的接口。

于是，有人提了一个[PR](https://github.com/facebook/react-native/pull/6478)给React Native, 实现了安卓版的`shouldStartLoadForRequest`. 这个PR来来回回有76个回复，结论就是如果你要在android里面做和iOS里面一样的事情，你就要自己写一个WebViewManager, 自己在java里面做处理。然后这个就PR被close了。


#为何不能给android实现同样的功能

#workaround
活人总不能给憋死，官方不支持这个功能，我们自己总得想点办法。
