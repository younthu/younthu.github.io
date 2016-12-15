---
layout: post
title: React Native 系列- A problem occurred starting process 'command 'node''
tags:
- React Native

category:
- 技术
---

在React Native 开发的时候编译android，`react-native run-android`,莫名遇到下面的 build failure:

~~~
:app:compileDebugAidl
:app:compileDebugRenderscript
:app:generateDebugBuildConfig
:app:mergeDebugShaders UP-TO-DATE
:app:compileDebugShaders UP-TO-DATE
:app:generateDebugAssets UP-TO-DATE
:app:mergeDebugAssets UP-TO-DATE
:app:generateDebugResValues
:app:generateDebugResources
:app:mergeDebugResources
:app:recordFilesBeforeBundleCommandDebug FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:recordFilesBeforeBundleCommandDebug'.
> A problem occurred starting process 'command 'node''

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.

BUILD FAILED

Total time: 9.91 secs
~~~

经过google, 找到workaround, 见github上[seeligd的回答](https://github.com/facebook/react-native/issues/6875#issuecomment-215854946):

~~~
cd android/
./gradlew --stop
~~~


这种坑要真跳进去也是很难出来的，使用新技术的过程就是不断踩坑填坑的过程。
