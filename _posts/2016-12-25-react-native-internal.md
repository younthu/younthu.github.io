---
layout: post
title: 深入理解react-native
categories:
- React Native
- 技术

tags: [react-native, internal, bridging, javascript, bridge]

excerpt: 本文会搜集一些文章，深入理解React Native和JavaScript的运行原理。
---
# React Native 架构
先上一副React Native 架构图，这是我在内部培训的时候画的一副图。

1. 绿色的是我们应用开发的部分。我们写的代码基本上都是在这一层。
1. 蓝色代表公用的跨平台的代码和工具引擎，一般我们不会动蓝色部分的代码。
1. 黄色代码平台相关的代码，做定制化的时候会添加修改代码。不跨平台，要针对平台写不同的代码。iOS写OC, android写java，web写js. 每个bridge都有对应的js文件，js部分是可以共享的。如果你想做三端融合，你就得理解这一个曾的东西。
1. 红色部分是系统平台的东西。红色上面有一个虚线，表示所有平台相关的东西都通过bridge隔离开来了。
1. 大部分情况下我们只用写绿色的部分，少部分情况下会写黄色的部分。你如果对基础架构和开源感兴趣，你可以写蓝色部分，然后尝试给那些大的开源项目提交代码。红色部分是独立于React Native的，不讨论。

![React Native架构](/assets/img/ReactNativeIntroduction.001.jpeg)


# Bridge
所有神奇的事情都发生在bridge层。

## 三个线程
React Native有三个重要的线程:
 1. Shadow queue. 布局引擎([yoga](https://facebook.github.io/yoga/))计算布局用的。
 1. Main thread. 主线程。就是操作系统的UI线程。无论是iOS还是android，一个进程都只有一个UI线程，我们常说的主线程. React Native所有UI绘制也是由同一个UI线程来维护。
 1. Javascript thread. javascript线程。 大家都知道javascript是单线程模型，event驱动的异步模型。React Native用了JS引擎，所以也必需有一个独立的js 线程. 所有JS和原生代码的交互都发生在这个线程里。死锁，异常也最容易发生在这个线程.

 可以看到Shadow queue是`queue`而不是`thread`, 在iOS里面`queue`是`thread`之上的一层抽象,GCD里面的一个概念，创建`queue`的时候可以指定是并行的还是串行的。也就是说，一个`queue`可能对应多个`thread`。

~~~
As mentioned above, every module will have it’s own GCD Queue by default, unless it specifies the queue it wants to run on, by implementing the -methodQueue method or synthesizing the  methodQueue property with a valid queue.
~~~

## 原生对象管理
待更新


## 消息机制(原生代码调用)

待更新
# 有用的图表
## 内部机制
![Js 调用机制](/assets/img/rnmessage.jpg)
## Js调用时序,[原文](https://tadeuzagallo.com/blog/react-native-bridge/)
![Call Cyle](/assets/img/js_call_graph.svg)

# 参考

1. [react-native bridge](https://tadeuzagallo.com/blog/react-native-bridge/), 解释了bridge的工作原理和作用, 所有native method导出到js都以`__rct_export__`打头.
1. [React Native deep dive](https://gist.github.com/nsimmons/d2ae624d2336f4ac436b), 深入理解React Native iOS执行过程, 讲解了React.js怎么参与进来的。
1. [JavascriptCore Apple doc](https://developer.apple.com/reference/javascriptcore)
1. [a deep dive into react-native](http://www.reactnative.com/a-deep-dive-into-react-native/), 作者貌似是React Native的开发成员, 讲解了一些技术选择和决策层面的东西, React.js Conf 2015 Keynote 2 - A Deep Dive into React Native
1. [Deep diving react-native debugging, medium.com](https://medium.com/@shaheenghiassy/deep-diving-react-native-debugging-ea406ed3a691#.am8igetoy), 深入讲解react-native调试的原理和流程, 对调试原理感兴趣的非常值得一看。要翻墙.
1. [Inspecting react-native bridge traffic, medium.com](https://medium.com/@shaheenghiassy/inspecting-react-native-s-bridge-traffic-631cb68837a9#.vnv2yo9oq), 查看 react-native bridge traffic， 挺有意思的一个技巧。要翻墙
1. [Debugging react native performance](https://medium.com/@jondot/debugging-react-native-performance-snoopy-and-the-messagequeue-fe014cd047ac) ,讲MessageQueue的， 性能调试相关。要翻墙。
1. [Debugging react-native packager, medium.com](https://medium.com/@shaheenghiassy/debugging-react-native-packager-3256b40988ab#.uv6kjdjbs), 调试[react-native packager](https://github.com/facebook/react-native/tree/master/packager), 也就是调试一个webpack类似的东西, 很有意思.
1. [React Native’s JavaScript Execution Contexts](https://medium.com/@shaheenghiassy/react-native-s-execution-context-d63e5d4930f4#.bpn3ffsz8)
1. [debugging react-native app](https://medium.com/reactnativeacademy/debugging-react-native-applications-6bff3f28c375#.pxdbsbi9a), 这个里面有提到react-native bridge 和底层native代码的调试机制. 还提到[react-native slow-log](https://github.com/jondot/react-native-slowlog), 一个time profiler库。还提到了JSEventLoopWatchdog, 还有[react perf](https://facebook.github.io/react/docs/perf.html)
1. [James Ide的blog](https://medium.com/@ji)
1. [How to create your own native bridge](https://medium.com/@kureevalexey/how-to-create-you-own-native-bridge-93a8d4a40bd2#.skvhi1cyh), 创建自己的bridge, 只能说老外钻研能力真强！这老哥是React Native core team的，牛逼! 里面用了[ChakraCore, microsoft edge的javascript引擎](https://github.com/Microsoft/ChakraCore), 样本项目源码在[github](https://github.com/Kureev/ExampleBridge), 还提到一个[抽象语法树(AST)生成器](http://esprima.org/demo/parse.html)
1. [Under the hood of React Native by Martin Konicek](https://speakerdeck.com/mkonicek/under-the-hood-of-react-native),
1. [Hot Reloading](https://facebook.github.io/react-native/blog/2016/03/24/introducing-hot-reloading.html) , Hot Reload的详细解释.
1. [Hot Module Replacement with webpack](https://webpack.github.io/docs/hot-module-replacement-with-webpack.html)
1. [Babel preset for react native](https://github.com/facebook/react-native/tree/master/babel-preset) , 代码编译转换用的是babel.
1. [Babel 配置文件使用方法](https://babeljs.io/docs/usage/babelrc/)
1. [Hot module Replacement 代码](https://github.com/facebook/react-native/blob/master/babel-preset/configs/hmr.js#L24-L31)
1. [Dan Abramov, Hot reloading in react的作者写的hot reloading](https://medium.com/@dan_abramov/hot-reloading-in-react-1140438583bf)
1. [Live editing with webpack](http://jlongster.com/Backend-Apps-with-Webpack--Part-III)
1. [Style loader](https://github.com/webpack-contrib/style-loader)
1. [讲webpack](http://jlongster.com/Backend-Apps-with-Webpack--Part-I)
1. [Webpack index](http://webpack.github.io/) , [Webpack Introduction](https://webpack.js.org/concepts/) webpack 官方文档.
1. [React Native通信机制](https://dev.qq.com/topic/5922977cd5cb29753024f981) ,
1. [React Native 通信机制详解](http://blog.cnbang.net/tech/2698/) , 这是比较老的一篇了, 还挺深的.
1. [探究react native通信机制](http://zjutkz.net/2016/05/03/%E5%85%B6%E5%AE%9E%E6%B2%A1%E9%82%A3%E4%B9%88%E5%A4%8D%E6%9D%82%EF%BC%81%E6%8E%A2%E7%A9%B6react-native%E9%80%9A%E4%BF%A1%E6%9C%BA%E5%88%B6/) , 里面提到js和native端的config映射，'A','B','C'映射。
1. [React Native internals - part 1 - MessageQueue](https://medium.com/@rotemmiz/react-native-internals-a-wider-picture-part-1-messagequeue-js-thread-7894a7cba868) , MessageQueue 是js和native代码的唯一接口和通道, mqt_js是js thread, mqt_native_modules是js调用native code用的线程。
1. [Debugging react native applications](https://medium.com/reactnativeacademy/debugging-react-native-applications-6bff3f28c375) ,
1. [官方文档，Native Js互调和回调](https://facebook.github.io/react-native/docs/native-modules-ios.html) , 有两种回调方式，一种是普通的error回调机制，另外一种是支持Promise的机制，重点在`RCTResponseSenderBlock`, `RCTPromiseResolveBlock`和`RCTPromiseRejectBlock`, `constantsToExport`, `sendEventWithName`, `suppportEvents`
1. [React Native与OC之间通信那些事儿](http://imweb.io/topic/57f6fdb848184f361288bc05) , 整个流程就是这样，简单概括下，差不多就是：JS函数调用转ModuleID/MethodID -> callback转CallbackID -> OC根据ID拿到方法 -> 处理参数 -> 调用OC方法 -> 回调CallbackID -> JS通过CallbackID拿到callback执行
1. [jsBridge 解析](http://supercocoa.github.io/2015/09/26/react_native_android_js_bridge/)
1. [ 简书 - 深入ReactNative 第一篇 通讯及消息循环代码剖析](http://www.jianshu.com/p/269b21958030) , 非常详细, 还有示例代码，非常值得深入看看.
1. [nshipster - JavaScriptCore](http://nshipster.cn/javascriptcore/) , 详解JavaScriptCore的运行机制, 这应该是一切的起点。
