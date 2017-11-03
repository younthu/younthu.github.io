---
layout: post
title: View.propTypes.style
categories:
- 技术
tags:
- React Native
- Debug
---

# 问题
`com.facebook.react.common.JavascriptException: undefined is not an object (evaluating 'l.propTypes.style'), `

# 重现步骤
安卓重现步骤非常简单，打包APK，部署到真机上运行就能重现bug。用命令`adb logcat`就能看到错误日志.

iPhone也是一样，打包release版，然后在真机上运行就能看到crash。但是，看不到日志。解决办法是从xcode运行release版(修改schema, 指定run的版本为release)，然后就能看到日志, 不能调试.

如果你想从xcode启动debug版，可以通过打包`main.jsbundle`文件，然后修改`main`函数里面的启动代码，指定debug模式下从本地bundle启动就可以调试c/c++/oc代码了。打包代码如下:

~~~
react-native bundle --entry-file index.ios.js --bundle-output main.jsbundle --dev false --platform ios  # 打包完成以后拖到xcode里面，指定target为你的项目.
~~~

# 分析

# 初步定位

#
