---
layout: post
title: React Native系列 - ListView白板workaround
categories:
- React Native
- 技术

tags:
- ListView
- 白板

excerpt: React Native <ListView /> 在数据量大的情况下刷新列表的时候会出现白板，网上有很多讨论，但是没有很好的解决办法。有一个比较稳定的workaround分享一下.
---

# 问题
React Native <ListView /> 在数据量大的情况下刷新列表的时候会出现白板，网上有很多讨论([react-native/issues/1831](https://github.com/facebook/react-native/issues/1831))，但是没有很好的解决办法。


# 原因
还没有仔细看底层代码，具体原因未知。只知道用手划一下ListView白板就会消失。在monitor里面看白板是真白板了，没有任何子元素, 但是datasource里面是有数据的。

#解决办法
每次刷新datasource的时候强制改变一下ListView的paddingTop(你也可以改其它属性，比如margin，border，backgroundcolor等)，强制刷新一下ListView. 样例代码如下:

注意，也有可能出现抖动都无法解决问题的情况，这种情况下请加上`removeClippedSubviews={false}`

~~~
constructor(){
  ...  
  this.refreshIndex = 0; // 初始化
}

render(){
  this.refreshIndex = this.refreshIndex + 1;// 在绘制前递增一下计数器
  padding = { paddingTop: this.refreshIndex % 2} // 每次取余，让padding在1和2之间抖动
  return (
    <ListView
    removeClippedSubviews={false}
    style={padding} 
    ...
    />)
  }
~~~

