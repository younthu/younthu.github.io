---
layout: post
title: React Native系列(7) - react-native-code-push使用
tags:
- React Native
category:
- 技术
---

# code-push plugin集成要点
这两天我们安卓app React Native版内测了，为了方便修复问题，我们使用了微软的[code-push](https://microsoft.github.io/code-push/)技术做热更新。

以前用这个做过React Native iOS的热更新,没有遇到啥问题。但是这次集成到安卓里面以后发现热更新不能工作。

花了点时间仔细看了一下文档，发现最新版集成方式变了，除了要把依赖包集成([Plugin Installation](https://microsoft.github.io/code-push/docs/react-native.html#link-5))进去以外，还要在JS层做一层封装或者配置，具体见[Plugin Usage](https://microsoft.github.io/code-push/docs/react-native.html#link-7), js代码片段摘录如下:

~~~
import codePush from "react-native-code-push";

class MyApp extends Component {
}

MyApp = codePush(MyApp);
~~~

# code-push cli常用命令

除了集成code-push sdk, 另外一个比较重要的就是code-push-cli的使用，注册用户账号，注册app，发布app，发布热更新都要走code-push-cli. 

常用命令记录如下:

1. code-push deployment history AppName Production
	
	查看Production上更新安装情况，参数Production可以换成Staging, 默认Staging
	
1. code-push release-react AppName android -d "Production" -m --description "Hide news and recommendation"
	
	发布更新到Production, 也可以用"Staging", 默认是Staging
2. code-push app add AppName, 
	
	在code-push上面注册新的app
3. code-push deployment ls AppName
	
	查看AppName下面所有环境的部署情况，相当于是 Production + Staging合并的结果。


