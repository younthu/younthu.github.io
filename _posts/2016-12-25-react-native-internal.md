---
layout: post
title: 深入理解react-native
categories:
 - 技术
tags: [react-native, internal, bridging, javascript, bridge]

excerpt: 本文尝试解密React Native技术内幕，剖析源码，深入理解React Native的技术原理。涵盖Bridge, MessageQueue, bundler, JavaScriptCore, 线程管理, 调试等话题，非应用层内容分享，适合有一定React Native基础的人阅读。
---

(2018-06-28, 更新)
这是一个深坑, 慎入！

本来是计划花半年时间写一本深入讲解react native技术细节的书的，内容(见下面TOC)和年度计划都拟好了，结果人算不如天算，计划拟好以后精力就转移到公司的情报挖掘(机器学习)项目上去了，移动端的代码已经接近两年没有怎么参与贡献了。

鉴于对这个感兴趣的同学好像还挺多的，我会间断性的继续补充内容，时间无保障。

本文末尾有非常多的参考链接，都是非常宝贵的资源，建议大家点开看看。

有兴趣在线沟通的同学可以联系我，加我微信:`evil_eve_live`, 请注明`深入理解react-native`.

# TOC(内容规划)

下面是我原计划要大写特写的内容，先诚招壮士一起做这个伟大的事业！欢迎加微信:`evil_eve_live`

1. 术语、简写约定
1. React Native简介
1. ReactJS 简介
1. JavascriptCore简介
1. 浏览器工作原理
1. React Native架构
    1. React Native、React和JavascriptCore的关系
    1. React Native主要组件
1. React Native 初始化
    1. 原生代码初始化
    1. Javascript环境初始化
    1. NativeModules加载
    1. NativeModules懒加载
1. React Native JS接口兼容(Polyfills)
    1. fetch替换
    1. CommonJS Require
    1. alert替换
    1. console.warning替换
    1. console.error替换
1. 线程讲解
    1. JS单线程和其背后的Native多线程
    1. JS的异步
    1. 三大线程
    1. 消息通信
1. 深入Bridge
    1. Bridge模块简介
    1. Bridge
    1. BridgeFactory
    1. JSCExecutor
    1. MessageQueue
1. Native调用JS代码
    1. 一般JS调用
    1. ReactNative里面的JS调用
    1. Native实现Promise
1. 原生对象管理
    1. 模块管理
    1. UIManager
    1. TagID
1. JS调用Native代码
    1. JS调用NativeModule代码
    1. JS回调Native代码
1. 渲染
    1. ReactJS渲染机制
    1. ReactFiber/ReactStack
    1. ReactNative渲染
1. 热加载
1. JS Reload
1. JS代码打包
    1. metro-bundler
    1. JSX
    1. babel
1. Websocket
1. YellowBox详解
1. RedBox详解
1. 调试菜单
1. 自定义一个原生控件

# 术语、简写约定

名词/缩写 | 解释
-----|---------
React Native | Facebook出的跨平台应用构建框架
ReactJS | Facebook出的Web UI JS框架，具有革新性的编程模式
React  | 若无特殊说明，React就是ReactJS
NodeJS | 基于JavaScript V8 Engine 的一个javascript runtime
RN | React Native的缩写
N2J | Native to JavaScript call
J2N | JavaScript to Native call
JavaScript | 我们常说的JavaScript脚本语言
JS  | JavaScript
JS Engine | JavaScript脚本解释执行引擎
JavaScriptCore | iOS/Android 平台上默认的JS Engine, 来源于Webkit, 无特殊说明情况下面，本文所有解释都会默认基于JavaScriptCore
JSC | JavaScriptCore
JSX | JavaScript的一个语言扩展，在JSX里面你可以用标记语言来描述React组件。不用JSX也可以写ReactJS,但是有JSX会高效很多
Bridge | React Native里面实现JS和原生代码互相调用的模块,该词指向两个概念，一个是RCTBridge,找个用来管理Bridge的组件，第二个是所有自定义的用于RN的原生功能。所有原生功能想要在JS里面使用都需要有定制的bridge来支持，所以我们把这个定制的模块(包括native和js端)也称之为bridge




# React Native简介
[React Native](https://facebook.github.io/react-native/)是Facebook出品的一个革新性的跨平台UI框架，跨平台不是它最大的亮点，它背后的[React]才应该是它的神奇说在，也是它革新所在。我在另外一篇博客[React Native系列(3) - props和state](http://blog.ilibrary.me/2016/10/14/react-native%E7%B3%BB%E5%88%97-3-props%E5%92%8Cstate)中有详细的分析。如果非要用一两句来总结它的伟大，那就是给把web开发中的无状态开发模式通过React实现了。 那些数不清的状态才是桌面应用和手机应用复杂的源头。

本文目的是通过源码分析，详细解释React Native框架的内部结构及运行原理。文章会比较长，组织上会尽可能由浅入深来讲。适合的读者对象是对React Native开发有一定基础的开发者。

分析的代码是基于0.50.3, 基于iOS平台的实现，包括原生的代码，js框架和打包器。从代码打包，react native初始化，js加载到运行以及错误处理。安卓平台的Java代码解读不在本文覆盖范围。

# ReactJS 简介
ReactJS是一个非常具有革新性的web UI框架，非常简单易用。它的virtual dom和data driven编程模式对现有的UI编程模式是一种颠覆, 极大简化了UI应用复杂状态的管理，非常值得大家去试一下。ReactJS配上Redux, 你会发现做复杂多状态的应用居然可以如此简单! React Native强大的原因就在于它是基于ReactJS的。相信读这篇文章的人大部分以对ReactJS有一定的了解，这里就不多说。

# JavaScriptCore
讲React Native之前，了解JavaScriptCore会有帮助，也是必要的。React Native的核心驱动力就来自于JS Engine. 你写的所有JS和JSX代码都会被JS Engine来执行, 没有JS Engine的参与，你是无法享受ReactJS给原生应用开发带来的便利的。在iOS上，默认的就是JavaScriptCore， iOS 7之后的设备都支持. iOS 不允许用自己的JS Engine. JavaScriptCore来自于WebKit, 所以，安卓上默认也是用JavaScriptCore.

所以，你深入了解React Native的第一站应该是 JavaScriptCore.

[nshipster.cn](nshipster.cn)有一篇[文章](http://nshipster.cn/javascriptcore/) 对JavaScriptCore的使用有一些简单的使用说明。读完这篇文章你会发现，JavaScriptCore使用起来是非常简单的，这得益于JavaScript的简洁设计。所以，本文不会花精力讲解JavaScriptCore。

相信有JavaScript和OC基础的同学只需要花大概30秒就可以把上面的这篇文章读完。

恭喜你！ 当你读完上面的那篇文章以后，你已经掌握了自己创造React Native框架的核心技术！一点也不夸张，JavaScriptCore在iOS平台上给React Native提供的接口也仅限于那几个接口，你弄明白了JavaScriptCore那几个接口, 理解整个React Native 剩下的事情都可以顺藤摸瓜来理解，就只是时间的问题了。

本文接下来要讲解的就是Facebook围绕这几个接口以及用一个React来颠覆整个native开发所做的精妙设计和封装。你如果想自己做一个基于JS Engine做一个类似React Native的框架出来，建议研究[JSPatch](https://github.com/bang590/JSPatch), 另外，还有一个外国朋友写了一个博客讲解[用Edge JS引擎自己动手写一个bridge](https://medium.com/@kureevalexey/how-to-create-you-own-native-bridge-93a8d4a40bd2#.skvhi1cyh). React Native的封装非常庞大，涉及了很多的话题，直接与JS Engine相关的不多。

# 浏览器工作原理
浏览器通过Dom Render来渲染所有的元素.
浏览器有一整套的UI控件，样式和功能都是按照html标准实现的。
浏览器能读懂html和css。
html告诉浏览器绘制什么控件(html tag)，css告诉浏览器每个类型的控件(html tag)具体长什么样。
浏览器的主要作用就是通过解析html来形成dom树，然后通过css来点缀和装饰树上的每一个节点。
UI的描述和呈现分离开了。
1. html文本描述了页面应该有哪些功能，css告诉浏览器该长什么样。
2. 浏览器引擎通过解析html和css，翻译成一些列的预定义UI控件，
3. 然后UI控件去调用操作系统绘图指令去绘制图像展现给用户。

在react native 里面，1和2是不变的，也是用html语言描述页面有哪些功能，然后stylesheet告诉浏览器引擎每个控件应该长什么样。并且和浏览器用的是同一个引擎。在步骤3里面UI控件不再是浏览器内置的控件，而是react native自己实现的一套UI控件（两套，android一套，ios一套），这个切换是在MessageQueque中进行的，并且还可以发现，他们tag也是不一样的。
# React Native 架构
先上一副React Native 架构图，这是我在内部培训的时候画的一副图。

1. 绿色的是我们应用开发的部分。我们写的代码基本上都是在这一层。
1. 蓝色代表公用的跨平台的代码和工具引擎，一般我们不会动蓝色部分的代码。
1. 黄色代码平台相关的代码，做定制化的时候会添加修改代码。不跨平台，要针对平台写不同的代码。iOS写OC, android写java，web写js. 每个bridge都有对应的js文件，js部分是可以共享的，写一份就可以了。如果你想做三端融合，你就得理解这一个东西。如果你要自己定制原生控件，你就得写bridge部分。
1. 红色部分是系统平台的东西。红色上面有一个虚线，表示所有平台相关的东西都通过bridge隔离开来了。
1. 大部分情况下我们只用写绿色的部分，少部分情况下会写黄色的部分。你如果对基础架构和开源感兴趣，你可以写蓝色部分，然后尝试给那些大的开源项目提交代码。红色部分是独立于React Native的，不讨论。

![React Native架构](/assets/img/ReactNativeIntroduction.001.jpeg)


## React Native、React和JavascriptCore的关系
React Native最重要的三个概念应该就是React Native、React和JavascriptCore.

理解这三者的关系以后大家就可以自己去深入研究React Native了。

React是一个纯JS库，所有的React代码和所有其它的js代码都需要JS Engine来解释执行。因为种种原因，浏览器里面的JS代码是不允许调用自定义的原生代码的，而React又是为浏览器JS开发的一套库，所以，比较容易理解的事实是React是一个纯JS库，它封装了一套Virtual Dom的概念，实现了数据驱动编程的模式，为复杂的Web UI实现了一种无状态管理的机制, 标准的HTML/CSS之外的事情，它无能为力。调用原生控件，驱动声卡显卡，读写磁盘文件，自定义网络库等等，这是JS/React无能为力的。

你可以简单理解为React是一个纯JS 函数， 它接受特定格式的字符串数据，输出计算好的字符串数据。

JS Engine负责调用并解析运行这个函数。

React Native呢？ 它比较复杂。复杂在哪里？前面我们说了React 是纯JS库，意味着React只能运行JS代码，通过JS Engine提供的接口(Html Tag)绘制html支持的那些元素，驱动有限的声卡显卡。简单点说, React只能做浏览器允许它做的事情, 不能调用原生接口， 很多的事情也只能干瞪眼。 React Native它可不一样，第一点，驱动关系不一样。前面我们说的是, JS Engine来解析执行React脚本, 所以，React由浏览器(最终还是JS Engine)来驱动. 到了React Native这里，RN的原生代码驱动JS Engine, 然后JS Engine解析执行React或者相关的JS代码，然后把计算好的结果返回给Native code. 然后, Native code 根据JS计算出来的结果驱动设备上所有能驱动的硬件。大家看好了，是所有的硬件。也就是说，到了RN这里，JS代码已经摆脱JS Engine(浏览器)的限制，可以调用所有原生接口啦！第二点, 它利用React的Virtual Dom和数据驱动编程概念，简化了我们原生应用的开发, 同时，它不由浏览器去绘制，只计算出绘制指令，最终的绘制还是由原生控件去负责，保证了原生的用户体验。

React组件结构(图文)

React Native组件结构(图文)

驱动硬件的能力决定能一个软件能做多大的事情，有多大的主控性。你如果研究过操作系统底层的一些东西或者汇编你就会明白，我们大部分时候写的代码是受限的代码，很多特权指令我们是没法使用的，很多设备我们是不允许直接驱动的。我们现在的编程里面几乎已经没有人提中断了，没有中断，何谈硬件驱动.

在一定程度上，React Native和NodeJS有异曲同工之妙。它们都是通过扩展JavaScript Engine, 使它具备强大的本地资源和原生接口调用能力，然后结合JavaScript丰富的库和社区和及其稳定的跨平台能力，把javascript的魔力在浏览器之外的地方充分发挥出来。

JavaScriptCore + ReactJS + Bridges 就成了React Native
#深入 Bridge
前面有提到, RN厉害在于它能打通JS和Native Code, 让JS能够调用丰富的原生接口,充分发挥硬件的能力, 实现非常复杂的效果,同时能保证效率和跨平台性。

打通RN任督二脉的关键组件就是Bridge.  在RN中如果没有Bridge, JS还是那个JS，只能调用JS Engine提供的有限接口，绘制标准html提供的那些效果,那些摄像头，指纹，3D加速,声卡, 视频播放定制等等，JS都只能流流口水，原生的、平台相关的、设备相关的效果做不了， 除非浏览器定制。

Bridge的作用就是给RN内嵌的JS Engine提供原生接口的扩展供JS调用。所有的本地存储、图片资源访问、图形图像绘制、3D加速、网络访问、震动效果、NFC、原生控件绘制、地图、定位、通知等都是通过Bridge封装成JS接口以后注入JS Engine供JS调用。理论上，任何原生代码能实现的效果都可以通过Bridge封装成JS可以调用的组件和方法, 以JS模块的形式提供给RN使用。

每一个支持RN的原生功能必须同时有一个原生模块和一个JS模块。Bridge会负责管理原生模块和对应JS模块之间的沟通, 通过Bridge, JS代码能够驱动所有原生接口，实现各种原生酷炫的效果。

RN中JS和Native分隔非常清晰，JS不会直接引用Native层的对象实例，Native也不会直接引用JS层的对象实例(Bridge层会保留几个最基础的方法和对象让JS和Native相互通信)。

Bridge 原生代码负责管理原生模块并生成对应的JS模块信息供JS代码调用。每个功能JS层的封装主要是针对ReactJS做适配，让原生模块的功能能够更加容易被用ReactJS调用。MessageQueue.js是Bridge在JS层的代理，所有JS2N和N2JS的调用都会经过MessageQueue.js来转发。JS和Native之间不存在任何指针传递，所有参数都是字符串传递。所有的instance都会被在JS和Native两边分别编号，然后做一个映射,然后那个数字/字符串编号会做为一个查找依据来定位跨界对象。

## Bridge各模块简介
RCTRootView, RCTRootContentView, RCTBridge, RCTBatchedBridge,RCTJavaScriptLoader,RCTContextExecutor, RCTModuleData, RCTModuleMethod,
### RCTRootView
RCTRootView是React Native加载的地方，每个项目的`AppDelegate.m`的`- (BOOL)application:didFinishLaunchingWithOptions:`里面都可以看到RCTRootView的初始化代码，RCTRootView初始化完成以后，整个React Native运行环境就已经初始化好了，JS代码也加载完毕，所有React的绘制都会有这个RCTRootView来管理。

RCTRootView做的事情如下:
1. 创建并且持有RCTBridge
1. 加载JS Bundle并且初始化JS运行环境.
1. 初始化JS运行环境的时候在App里面显示loadingView, 注意不是屏幕顶部的那个下拉悬浮进度提示条.
   RN第一次加载之后每次启动非常快，很少能意识到这个加载过程了。loadingView默认情况下为空, 也就是默认是没有效果的。loadingView可以被自定义，直接覆盖RCTRootView.loadingView就可以了.开发模式下RN app第一次启动因为需要完整打包整个js所以可以很明显看到加载的过程，加载第一次以后就看不到很明显的加载过程了，可以执行下面的命令来触发重新打包整个js来观察loadingView的效果
    ` watchman watch-del-all && rm -rf node_modules/ && yarn install && yarn start -- --reset-cache`, 然后杀掉app重启你就会看到一个很明显的进度提示.
1. JS运行环境准备好以后把加载视图用RCTRootContentView替换加载视图.
1. 所有准备工作就绪以后调用`AppRegistry.runApplication`正式启动RN JS代码，从Root Component(<AppContainer />)开始UI绘制。

一个App可以有多个RCTRootView, 初始化的时候需要手动传输`Bridge`做为参数，全局可以有多个RCTRootView, 但是只能有一个`Bridge`.

如果你做过React Native和原生代码混编，你会发现混编就是把`AppDelegate`里面那段初始化`RCTRootView`的代码移动到需要混编的地方，然后把`RCTRootView`做为一个普通的subview来加载到原生的view里面去，非常简单。不过这地方也要注意处理好单`Bridge`实例的问题，同时，混编里面要注意`RCTRootView`如果销毁过早可能会引发JS回调奔溃的问题。

### RCTRootContentView
RCTRootContentView reactTag在默认情况下为1. 在Xcode view Hierarchy debugger 下可以看到，最顶层为RCTRootView, 里面嵌套的是`RCTRootContentView`, 从RCTRootContentView开始，每个View都有一个reactTag.

RCTRootView继承自UIView, RCTRootView主要负责初始化JS Environment和React代码，然后管理整个运行环境的生命周期。 RCTRootContentView继承自RCTView, RCTView继承自UIView, RCTView封装了React Component Node更新和渲染的逻辑， RCTRootContentView会管理所有react ui components. RCTRootContentView同时负责处理所有touch事件.

### RCTBridge
1. 负责加载各个Bridge模块供JS调用
1. 找到并注册所有实现了`RCTBridgeModule` protocol的类, 供JS后期使用.
1. 创建和持有 RCTBatchedBridge

### RCTBatchedBridge
1. 负责Native和JS之间的相互调用(消息通信)
1. 持有JSExecutor
1. 实例化所有在RCTBridge里面注册了的native node_modules
1. 创建JS运行环境, 注入native hooks 和modules, 执行 JS bundle script
1. 管理JS run loop,  批量把所有JS到native的调用翻译成native invocations
1. 批量管理原生代码到JS的调用，把这些调用翻译成JS消息发送给JS executor

### RCTJavaScriptLoader
1. 从指定的地方(bundle, http server)加载 script bundle
1. 把加载完成的脚本用string的形式返回
1. 处理所有获取代码、打包代码时遇到的错误

### RCTContextExecutor
1. 管理和执行所有N2J调用

### RCTModuleData
1. 收集所有桥接模块的信息，供注入到JS运行环境

### RCTModuleMethod
1. 翻译所有J2N call，然后执行对应的native方法。

## BridgeFactory

## JSCExecutor
## MessageQueue
这是核心中的核心。整个react native对浏览器内核是未做任何定制的，完全依赖浏览器内核的标准接口在运作。它怎么实现UI的完全定制的呢？它实际上未使用浏览器内核的任何UI绘制功能，注意是未使用UI绘制功能。它利用javascript引擎强大的DOM操作管理能力来管理所有UI节点，每次刷新前把所有节点信息更新完毕以后再给yoga做排版，然后再调用原生组件来绘制。javascript是整个系统的核心语言。

我们可以把浏览器看成一个盒子，javascript引擎是盒子里面的总管，DOM是javascript引擎内置的，javascript和javascript引擎也是无缝链接的。react native是怎么跳出这个盒子去调用外部原生组件来绘制UI的呢？秘密就在MessageQueue。

javascript引擎对原生代码的调用都是通过一套固定的接口来实现，这套接口的主要作用就是记录原生接口的地址和对应的javascript的函数名称，然后在javascript调用该函数的时候把调用转发给原生接口
# React Native 初始化
## 原生代码初始化
## Javascript环境初始化
## NativeModules加载
## NativeModules懒加载
1. React Native JS接口兼容(Polyfills)
    1. fetch替换
    1. CommonJS Require
    1. alert替换
    1. console.warning替换
    1. console.error替换
1. 线程讲解
    1. JS单线程和其背后的Native多线程
    1. JS的异步
    1. 三大线程
    1. 消息通信
1. Native调用JS代码
    1. 一般JS调用
    1. ReactNative里面的JS调用
    1. Native实现Promise
1. 原生对象管理
    1. 模块管理
    1. UIManager
    1. TagID
1. JS调用Native代码
    1. JS调用NativeModule代码
    1. JS回调Native代码
1. 渲染
    1. ReactJS渲染机制
    1. ReactFiber/ReactStack
    1. ReactNative渲染
1. 热加载
1. JS Reload
1. JS代码打包
    1. metro-bundler
    1. JSX
    1. babel
1. Websocket
1. YellowBox详解
1. RedBox详解
1. 调试菜单
1. 自定义一个原生控件
1. SplashScreen工作逻辑

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
![Executor Environment](/assets/img/rnexecutor.jpg), [原文](https://medium.com/@shaheenghiassy/react-native-s-execution-context-d63e5d4930f4)
![Js to Java call](/assets/img/rnJs2JavaCall.jpg), 出处[bugly](https://dev.qq.com/topic/5922977cd5cb29753024f981)
![消息循环](/assets/img/rnMessageLoop.png), [出处](http://www.jianshu.com/p/269b21958030)

# 打包

# 参考

1. [react-native bridge](https://tadeuzagallo.com/blog/react-native-bridge/), 解释了bridge的工作原理和作用, 所有native method导出到js都以`__rct_export__`打头.
1. [React Native deep dive](https://gist.github.com/nsimmons/d2ae624d2336f4ac436b), 深入理解React Native iOS执行过程, 讲解了React.js怎么参与进来的, 对每个主要的类的角色有详细的解释。
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
1. [React Native internals - part 1 - MessageQueue](https://medium.com/@rotemmiz/react-native-internals-a-wider-picture-part-1-messagequeue-js-thread-7894a7cba868) , MessageQueue 是js和native代码的唯一接口和通道, mqt_js是js thread, mqt_native_modules是js调用native code用的线程。有卡顿演示代码.
1. [Debugging react native applications](https://medium.com/reactnativeacademy/debugging-react-native-applications-6bff3f28c375) ,
1. [官方文档，Native Js互调和回调](https://facebook.github.io/react-native/docs/native-modules-ios.html) , 有两种回调方式，一种是普通的error回调机制，另外一种是支持Promise的机制，重点在`RCTResponseSenderBlock`, `RCTPromiseResolveBlock`和`RCTPromiseRejectBlock`, `constantsToExport`, `sendEventWithName`, `suppportEvents`
1. [React Native与OC之间通信那些事儿](http://imweb.io/topic/57f6fdb848184f361288bc05) , 整个流程就是这样，简单概括下，差不多就是：JS函数调用转ModuleID/MethodID -> callback转CallbackID -> OC根据ID拿到方法 -> 处理参数 -> 调用OC方法 -> 回调CallbackID -> JS通过CallbackID拿到callback执行
1. [jsBridge 解析](http://supercocoa.github.io/2015/09/26/react_native_android_js_bridge/)
1. [ 简书 - 深入ReactNative 第一篇 通讯及消息循环代码剖析](http://www.jianshu.com/p/269b21958030) , 非常详细, 还有示例代码，非常值得深入看看.有懒加载的解释。
1. [nshipster - JavaScriptCore](http://nshipster.cn/javascriptcore/) , 详解JavaScriptCore的运行机制, 这应该是一切的起点。
1. [扩展React Native Dev Menu](https://medium.com/capriza-engineering/extending-react-natives-dev-menu-c084fc93717d), 改造React Native的Dev Menu.
1. [JavaScript环境](https://reactnative.cn/docs/0.50/javascript-environment.html)
1. [Storybook独立组件编写工具](https://github.com/storybooks/storybook)
1. [React.JS internals: Virtual DOM](https://medium.com/@rajikaimal/react-js-internals-virtual-dom-d054347b7f00)
1. [Virtual DOM in ReactJS](https://hackernoon.com/virtual-dom-in-reactjs-43a3fdb1d130), 有解释为何更新RealDom会很慢.
1. [React Reconciliation](https://reactjs.org/docs/reconciliation.html), 官方对Reconcilation的讲解.
1. [Browser Internals](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)
1. [React Components, Elements, and Instances](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html), 用plain js解释了components.
1. [Haul - React Native developing command line tool](https://github.com/callstack/haul)
1. [Ignite - 模板生成工具](https://github.com/infinitered/ignite)
1. [React Native Roadmap](https://github.com/facebook/react-native/wiki/Roadmap)
1. [React Native Release Notes](https://github.com/facebook/react-native/releases)
1. [关于shadow view的解释](http://www.hotobear.com/?p=1015)
1. [RCTWebViewExecutor最后一次在0.15里面出现](https://github.com/facebook/react-native/tree/0.15-stable/React/Executors)
1. [react和webpack](http://www.infoq.com/cn/articles/react-and-webpack)
1. [webpack打包机制](https://github.com/happylindz/blog/issues/6)
