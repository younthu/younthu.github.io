---
layout: post
title: React Native系列(2) - undefined this.state
tags:
- React Native
- Javascript
- undefined
categories:
- 技术
- React Native
excerpt: 很多新手，特别是非web前端的新手，进来的时候经常遇到这种问题:`undefined is not an object (evaluating 'this.state.username')`, 这个问题多半是由于javascript特殊的context绑定机制造成this指向了其它的对象造成的。

---
很多新手，特别是非web前端的新手，进来的时候经常遇到这种问题:

`
undefined is not an object (evaluating 'this.state.username')
`

如果我没有猜错，抛错的函数应该被当做一个函数指针传给某个回调函数了。正确的做法是在函数做参数的时候bind(this).

## 下面用ListView的回调函数绑定来说明正确用法

请看代码注释

```javascript
_renderRow() {
	console.log(this.state.username);
}

_renderFooter(){
	console.log(this)// 不会抛错，this指向window对象
	console.log(this.state) // 抛错，window对象没有state属性
}

render(){
	return(
		<ListView dataSource={this.state.dataSource}

     		renderRow={this._renderRow.bind(this)} // 正确用法

     		onEndReached={this._fetchNextPage.bind(this)} // 正确用法
         	renderFooter={this._renderFoot}// 错误用法，忘记了bind(this)
                            />);
```

## 原理解析

所有面向对象的实例函数被调用的时候都会传递一个隐式参数this, 在javascript里面把函数作为参数传递的时候会丢掉这个this, 也就是这个函数的上下文丢失了。这种情况下this 怎么办？

this会指向全局window对象，window是没有state属性的。state属性是React.Component的默认属性，不是window的默认属性，所以访问this.state会出错:

```javascript
_renderFooter() {
	console.log(this)// 不会抛错，this指向window对象
	console.log(this.state) // 抛错，window对象没有state属性
}
```

要想访问在_renderFooter里面正确访问'this.state', 有两种方法，一种是this._renderFooter.bind(this), 一种是用闭包.


### 方法1, bind(this)
可以通过显示给函数对象bind this指针的方法来解决this指向不对的问题。

```javascript
render(){
	return(
		<ListView dataSource={this.state.dataSource}

     		renderRow={this._renderRow.bind(this)} // 正确用法

     		onEndReached={this._fetchNextPage.bind(this)} // 正确用法
         	renderFooter={this._renderFoot().bind(this)}
                            />);
```

### 方法2, 闭包

还可以通过传递匿名函数(闭包)作为函数参数，闭包里面调用对应的函数时会自动绑定正确的this指针.

```javascript
render(){
	return(
		<ListView dataSource={this.state.dataSource}

     		renderRow={this._renderRow.bind(this)} // 正确用法

     		onEndReached={this._fetchNextPage.bind(this)} // 正确用法
         	renderFooter={()=>{
         	// 闭包，闭包把当前的上下文(this)给记录了下来。
         	this._renderFoot()
         	}}
                            />);
```

## 参考
[JavaScript对象模型-执行模型](http://www.cnblogs.com/riccc/archive/2008/02/15/javascript-object-model-execution-model.html), 讲解非常详细

[JavaScript系列----作用域链和闭包](http://www.cnblogs.com/renlong0602/p/4398883.html)
