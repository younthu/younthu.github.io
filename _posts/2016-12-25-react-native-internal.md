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
 
## 原生对象管理
待更新


## 消息机制(原生代码调用)

待更新

# 参考

1. [react-native bridge](https://tadeuzagallo.com/blog/react-native-bridge/), 解释了bridge的工作原理和作用.
1. [React Native deep dive](https://gist.github.com/nsimmons/d2ae624d2336f4ac436b), 深入理解React Native iOS执行过程, 讲解了React.js怎么参与进来的。
1. [JavascriptCore Apple doc](https://developer.apple.com/reference/javascriptcore)
1. [a deep dive into react-native](http://www.reactnative.com/a-deep-dive-into-react-native/), 作者貌似是React Native的开发成员, 讲解了一些技术选择和决策层面的东西, React.js Conf 2015 Keynote 2 - A Deep Dive into React Native
1. [Deep diving react-native debugging, medium.com](https://medium.com/@shaheenghiassy/deep-diving-react-native-debugging-ea406ed3a691#.am8igetoy), 深入讲解react-native调试的原理和流程, 对调试原理感兴趣的非常值得一看。要翻墙.
1. [Inspecting react-native bridge traffic, medium.com](https://medium.com/@shaheenghiassy/inspecting-react-native-s-bridge-traffic-631cb68837a9#.vnv2yo9oq), 查看 react-native bridge traffic， 挺有意思的一个技巧。要翻墙
1. [Debugging react-native packager, medium.com](https://medium.com/@shaheenghiassy/debugging-react-native-packager-3256b40988ab#.uv6kjdjbs), 调试[react-native packager](https://github.com/facebook/react-native/tree/master/packager), 也就是调试一个webpack类似的东西, 很有意思.
1. [React Native’s JavaScript Execution Contexts](https://medium.com/@shaheenghiassy/react-native-s-execution-context-d63e5d4930f4#.bpn3ffsz8)
1. [debugging react-native app](https://medium.com/reactnativeacademy/debugging-react-native-applications-6bff3f28c375#.pxdbsbi9a), 这个里面有提到react-native bridge 和底层native代码的调试机制. 还提到[react-native slow-log](https://github.com/jondot/react-native-slowlog), 一个time profiler库。还提到了JSEventLoopWatchdog, 还有[react perf](https://facebook.github.io/react/docs/perf.html)
1. [James Ide的blog](https://medium.com/@ji)
1. [How to create your own native bridge](https://medium.com/@kureevalexey/how-to-create-you-own-native-bridge-93a8d4a40bd2#.skvhi1cyh), 创建自己的bridge, 只能说老外钻研能力真强！这老哥是React Native core team的，牛逼! 里面用了[ChakraCore, microsoft edge的javascript引擎](https://github.com/Microsoft/ChakraCore), 样本项目源码在[github](https://github.com/Kureev/ExampleBridge), 还提到一个[抽象语法树(AST)生成器](http://esprima.org/demo/parse.html)
1. [Under the hood of React Native by Martin Konicek](https://speakerdeck.com/mkonicek/under-the-hood-of-react-native),
