---
layout: post
title: 深入理解react-native
categories:
- react-native
- 技术

tags:
- react-native
- internal
- bridging
- javascript
- bridge
---

# 参考
[react-native bridge](https://tadeuzagallo.com/blog/react-native-bridge/), 解释了bridge的工作原理和作用.

[React Native deep dive](https://gist.github.com/nsimmons/d2ae624d2336f4ac436b), 深入理解React Native iOS执行过程, 讲解了React.js怎么参与进来的。

[JavascriptCore Apple doc](https://developer.apple.com/reference/javascriptcore)

[a deep dive into react-native](http://www.reactnative.com/a-deep-dive-into-react-native/), 作者貌似是React Native的开发成员, 讲解了一些技术选择和决策层面的东西, React.js Conf 2015 Keynote 2 - A Deep Dive into React Native

[Deep diving react-native debugging, medium.com](https://medium.com/@shaheenghiassy/deep-diving-react-native-debugging-ea406ed3a691#.am8igetoy), 深入讲解react-native调试的原理和流程, 对调试原理感兴趣的非常值得一看。要翻墙.

[Inspecting react-native bridge traffic, medium.com](https://medium.com/@shaheenghiassy/inspecting-react-native-s-bridge-traffic-631cb68837a9#.vnv2yo9oq), 查看 react-native bridge traffic， 挺有意思的一个技巧。要翻墙

[Debugging react-native packager, medium.com](https://medium.com/@shaheenghiassy/debugging-react-native-packager-3256b40988ab#.uv6kjdjbs), 调试[react-native packager](https://github.com/facebook/react-native/tree/master/packager), 也就是调试一个webpack类似的东西, 很有意思.

[React Native’s JavaScript Execution Contexts](https://medium.com/@shaheenghiassy/react-native-s-execution-context-d63e5d4930f4#.bpn3ffsz8)

[debugging react-native app](https://medium.com/reactnativeacademy/debugging-react-native-applications-6bff3f28c375#.pxdbsbi9a), 这个里面有提到react-native bridge 和底层native代码的调试机制. 还提到[react-native slow-log](https://github.com/jondot/react-native-slowlog), 一个time profiler库。还提到了JSEventLoopWatchdog, 还有[react perf](https://facebook.github.io/react/docs/perf.html)

[James Ide的blog](https://medium.com/@ji)

[How to create your own native bridge](https://medium.com/@kureevalexey/how-to-create-you-own-native-bridge-93a8d4a40bd2#.skvhi1cyh), 创建自己的bridge, 只能说老外钻研能力真强！这老哥是React Native core team的，牛逼! 里面用了[ChakraCore, microsoft edge的javascript引擎](https://github.com/Microsoft/ChakraCore), 样本项目源码在[github](https://github.com/Kureev/ExampleBridge), 还提到一个[抽象语法树(AST)生成器](http://esprima.org/demo/parse.html)
