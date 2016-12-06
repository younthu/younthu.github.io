---
layout: post
title: 编译私有React-Native代码
---
我们在开发中遇到一个HTTP2的问题，React Native安卓客户端在和HTTP2支持的服务器通讯的过程中会有crash,见[React-Native HTTP2 issue](https://github.com/facebook/react-native/issues/11283)

[How to build private build](https://github.com/facebook/react-native/issues/11284)

由于时间紧急，发布期限已经拖了好久了，没法等待官方解决方案，只能在本地做修复然后发布。

### 编译私有React-Native
针对android，React-Native有[官方指导说明](https://facebook.github.io/react-native/docs/android-building-from-source.html).

过程中遇到不少坑，记录如下:

1. **坑1，boost下载失败**

	说明文档里面指定的 gradle-download-task版本有问题， 要用最新版，老版的2.0.0会抛403下载失败，导致编译通不过。测试de.undercouch:gradle-download-task:3.1.2可以工作。

~~~
classpath 'de.undercouch:gradle-download-task:3.1.2'
~~~

1. **NDK版本不对导致编译失败**
	
	[文档](https://facebook.github.io/react-native/docs/android-building-from-source.html)里面指定NDK是android-ndk-r10e, 我用brew搜不到r10e,于是装了一个13b, 结果编译错误，只能乖乖手动下载r10e的包然后解压，然后在.zshrc里面设置ANDROID_NDK路径
	
~~~
	export ANDROID_NDK=/Users/your_home/android-ndk/android-ndk-r10e
~~~
	
用android-ndk-r13b编译抛错如下:
	
~~~
	:ReactAndroid:buildReactNdkLib
[armeabi-v7a] Compile++      : reactnativejni <= Dummy.cpp
[armeabi-v7a] Compile++      : reactnativejnifb <= CatalystInstanceImpl.cpp
make: [armeabi-v7a] Compile++      : reactnativejnifb <= CxxModuleWrapper.cpp
/usr/local/Cellar/android-ndk/r13b/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-g++: No such file or directory
make: /usr/local/Cellar/android-ndk/r13b/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-g++: No such file or directory
[armeabi-v7a] Compile++      : reactnativejnifb <= JExecutorToken.cpp
make: /usr/local/Cellar/android-ndk/r13b/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-g++: No such file or directory
make: /usr/local/Cellar/android-ndk/r13b/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-g++: No such file or directory
make: *** [/Users/andrewy/sourcecode/github/dgandroid3/ReactNative/dgAndroid/node_modules/react-native/ReactAndroid/build/tmp/buildReactNdkLib/local/armeabi-v7a/objs/reactnativejni/Dummy.o] Error 1
make: *** Waiting for unfinished jobs....
make: *** [/Users/andrewy/sourcecode/github/dgandroid3/ReactNative/dgAndroid/node_modules/react-native/ReactAndroid/build/tmp/buildReactNdkLib/local/armeabi-v7a/objs/reactnativejnifb/CxxModuleWrapper.o] Error 1
make: *** [/Users/andrewy/sourcecode/github/dgandroid3/ReactNative/dgAndroid/node_modules/react-native/ReactAndroid/build/tmp/buildReactNdkLib/local/armeabi-v7a/objs/reactnativejnifb/CatalystInstanceImpl.o] Error 1
make: *** [/Users/andrewy/sourcecode/github/dgandroid3/ReactNative/dgAndroid/node_modules/react-native/ReactAndroid/build/tmp/buildReactNdkLib/local/armeabi-v7a/objs/reactnativejnifb/JExecutorToken.o] Error 1
[armeabi-v7a] Compile++      : reactnativejnifb <= JMessageQueueThread.cpp
make: /usr/local/Cellar/android-ndk/r13b/toolchains/arm-linux-androideabi-4.8/prebuilt/darwin-x86_64/bin/arm-linux-androideabi-g++: No such file or directory
make: *** [/Users/andrewy/sourcecode/github/dgandroid3/ReactNative/dgAndroid/node_modules/react-native/ReactAndroid/build/tmp/buildReactNdkLib/local/armeabi-v7a/objs/reactnativejnifb/JMessageQueueThread.o] Error 1
:ReactAndroid:buildReactNdkLib FAILED
FAILURE: Build failed with an exception.
* What went wrong:
Execution failed for task ':ReactAndroid:buildReactNdkLib'.
> Process 'command '/usr/local/Cellar/android-ndk/r13b/ndk-build'' finished with non-zero exit value 2
~~~

1. react-native-fetch-blob编译失败 
	
	（<strike>实际上把后面的react-native 重复的问题解决后这个问题会自动解决,可以跳过</strike>）
	
	ndk的问题解决以后，react-native-fetch-blob编译又失败了.提示okhttp3找不到。
	
	解决方法: 把react-native-fetch-blob/android/build.gradle下面的
	
~~~
	compile 'com.facebook.react:react-native:+'
~~~
	
	替换成
	
~~~
	compile project(':ReactAndroid')
~~~
	
1. react-native 重复的问题

   前面所有问题解决以后还可能会遇到下面的问题
   
~~~
   :app:processDebugResources FAILED

	FAILURE: Build failed with an exception.

	* What went wrong:
	Execution failed for task 	':app:processDebugResources'.
	> Error: more than one library with package 	name 'com.facebook.react'
~~~

解决办法:
	在your_project_root/android/app/build.gradle里面添加如下配置(放在dependency同一层级即可)
	
~~~
	configurations.all {
    exclude group: 'com.facebook.react', module: 'react-native'
	}
~~~

1. Boost 下载失败的问题。

  首先，你可以尝试更新 gradle-download-task 到 3.1.2，这可以解决gradle-download-task 2.0.0下载时抛403的问题。
  
  然后，如果你网络比较差，经常下到一半超时断线,你可以在浏览器里面把boost下载下来，然后，命令行里面boost开始下载以后CTRL+C中断，然后用浏览器里面下载的boost覆盖目标boost. 我这边是
  
~~~
  node_modules/react-native/ReactAndroid/build/downloads/boost_1_57_0.zip
~~~
  
再次运行react-native run-android
 
编译成功！

然后你的app跑起来的时候可能会遇到下面的问题, 心累！:

~~~
Shutting down VM
 FATAL EXCEPTION: main
 Process: com.dealglobe, PID: 2162
 java.lang.UnsatisfiedLinkError: couldn't find DSO to load: libyoga.so
 	at com.facebook.soloader.SoLoader.loadLibraryBySoName(SoLoader.java:314)
 	at com.facebook.soloader.SoLoader.loadLibrary(SoLoader.java:247)
 	at com.facebook.csslayout.YogaNode.<clinit>(YogaNode.java:24)
 	at com.facebook.react.uimanager.ReactShadowNode.<init>(ReactShadowNode.java:82)
 	at com.facebook.react.uimanager.UIImplementation.createRootShadowNode(UIImplementation.java:84)
 	at com.facebook.react.uimanager.UIImplementation.registerRootView(UIImplementation.java:120)
 	at com.facebook.react.uimanager.UIManagerModule.addMeasuredRootView(UIManagerModule.java:198)
 	at com.facebook.react.XReactInstanceManagerImpl.attachMeasuredRootViewToInstance(XReactInstanceManagerImpl.java:803)
 	at com.facebook.react.XReactInstanceManagerImpl.setupReactContext(XReactInstanceManagerImpl.java:778)
 	at com.facebook.react.XReactInstanceManagerImpl.access$700(XReactInstanceManagerImpl.java:110)
 	at com.facebook.react.XReactInstanceManagerImpl$ReactContextInitAsyncTask.onPostExecute(XReactInstanceManagerImpl.java:224)
 	at com.facebook.react.XReactInstanceManagerImpl$ReactContextInitAsyncTask.onPostExecute(XReactInstanceManagerImpl.java:193)
 	at android.os.AsyncTask.finish(AsyncTask.java:632)
 	at android.os.AsyncTask.access$600(AsyncTask.java:177)
 	at android.os.AsyncTask$InternalHandler.handleMessage(AsyncTask.java:645)
 	at android.os.Handler.dispatchMessage(Handler.java:102)
 	at android.os.Looper.loop(Looper.java:135)
 	at android.app.ActivityThread.main(ActivityThread.java:5221)
 	at java.lang.reflect.Method.invoke(Native Method)
 	at java.lang.reflect.Method.invoke(Method.java:372)
 	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:899)
 	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:694)
~~~

经证实，这个是因为react-native代码不稳定造成的，切换到0.40-stable问题就消失了。
