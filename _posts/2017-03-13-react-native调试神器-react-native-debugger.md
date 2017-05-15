---
layout: post
title: React Native调试神器-React Native Debugger

categories:
- 技术

tags:
- React Native
- 调试
- React Native Debugger
- Debug
---

今天想介绍一下React Native调试神器, [React Native Debugger](https://github.com/jhen0409/react-native-debugger), 它集成了好几开源工具，包括[React DevTools](https://github.com/facebook/react-devtools/tree/master/packages/react-devtools-core#requirereact-devtools-corestandalone)、[react-devtools-core](https://github.com/facebook/react-devtools/tree/master/packages/react-devtools-core#requirereact-devtools-corestandalone)、[Redux DevTools](https://github.com/gaearon/redux-devtools)和[Remote Redux DevTools](https://github.com/zalmoxisus/remote-redux-devtools)


#  集成步骤
React Native Debugger本身不提供客户端sdk，它通过集成上面提到的几个debugger工具的调试端代码，达到支持多种调试功能的目的。你需要自己手动去分别集成客户端代码。

1. 集成[redux-devtools-extension](https://github.com/gaearon/redux-devtools) 到客户端。

~~~
import { composeWithDevTools } from 'redux-devtools-extension';
const middleware = composeWithDevTools({})(applyMiddleware(thunk, logger));
~~~

1. 打开React Native Debugger，注意关闭任何打开着的网页debugger-ui页面, 运行app，打开远程js调试。
1. 然后你就可以看到类似下面的界面了

![React Native Debugger](https://cloud.githubusercontent.com/assets/3001525/15636231/9e47d322-262a-11e6-8326-9a05fc73adec.png)



如果有问题，建议参考React Native Debugger的[样例工程代码](https://github.com/jhen0409/react-native-debugger-redux-example)
# 功能
1. 查看所有的action、state记录
1. 回放，也就是time traverse
1. 撤销、应用某个action
1. 查看app ui hierachy
1. 导入导出action, store记录

# 问题
使用的时候遇到以下问题。

1. 这个工具不能编辑action然后dispatch。可能是编辑器有问题。
1. 无法查看$r变量, 这个变量是啥？
