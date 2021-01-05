---
layout: post
title: 微信小程序decorator
date: 2021-01-03 19:40 +0800
excerpt: 微信小程序现在是支持decorator的，通过babel插件支持的。
---

# decorator介绍
现在的decorator是通过babel插件`babel-plugin-transform-decorators`支持的.

## ES5 的 Object.defineProperty 方法 。
ES7 中的 decorator 同样借鉴了python，不过依赖于 ES5 的 Object.defineProperty 方法 。

该方法允许精确添加或修改对象的属性。通过赋值来添加的普通属性会创建在属性枚举期间显示的属性（for...in 或 Object.keys 方法）， 这些值可以被改变，也可以被删除。这种方法允许这些额外的细节从默认值改变。默认情况下，使用 Object.defineProperty() 添加的属性值是不可变的。

### 语法

~~~js
Object.defineProperty(obj, prop, descriptor)
~~~

1. obj：要在其上定义属性的对象。
1. prop：要定义或修改的属性的名称。
1. descriptor：将被定义或修改的属性描述符。
1. 返回值：被传递给函数的对象。

在ES6中，由于 Symbol类型 的特殊性，用 Symbol类型 的值来做对象的key与常规的定义或修改不同，而Object.defineProperty 是定义 key为 Symbol 的属性的方法之一。
## 函数decorator
下面来个例子简单介绍一下函数装饰器:

~~~js
class Person { 
    @readonly 
    name() { 
        return `${this.first} ${this.last}` 
    } 
}
~~~

下面是decorator的定义。
~~~js
function readonly(target, name, descriptor){ 
    // descriptor对象原来的值如下 
    // { 
    // value: specifiedFunction, 
    // enumerable: false, 
    // configurable: true, 
    // writable: true 
    // }; 
    descriptor.writable = false; 
    return descriptor; 
} 
readonly(Person.prototype, 'name', descriptor); // 类似于 Object.defineProperty(Person.prototype, 'name', descriptor);
~~~

** 关于函数decorator参数定义: **
1. 装饰器第一个参数是 类的原型对象，上例是 Person.prototype，装饰器的本意是要“装饰”类的实例，但是这个时候实例还没生成，所以只能去装饰原型（这不同于类的装饰，那种情况时target参数指的是类本身）；
1. 第二个参数是 所要装饰的属性名
1. 第三个参数是 该属性的描述对象

## 类decorator


再来一个类装饰器:

~~~js
@testable 
class MyTestableClass { 
    // ... 
    } 
function testable(target) 
{ 
    target.isTestable = true;  
} 
MyTestableClass.isTestable // true
~~~

上面代码中，`@testable` 就是一个装饰器。它修改了 `MyTestableClass`这 个类的行为，为它加上了静态属性`isTestable`。
`testable` 函数的参数 `target` 是 `MyTestableClass` 类本身。

类装饰器传参数

~~~js
function testable(isTestable) { 
    return function(target) { 
        target.isTestable = isTestable; 
    } 
} 
@testable(true) 
class MyTestableClass {} 
MyTestableClass.isTestable // true @testable(false) class MyClass {} MyClass.isTestable // false
~~~
上面代码中，装饰器 testable 可以接受参数，这就等于可以修改装饰器的行为。

注意，装饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。这意味着，装饰器能在编译阶段运行代码。也就是说，装饰器本质就是编译时执行的函数。

# 微信小程序使用样例
下面是一个检查登录状态的decorator, 如果未登录，则跳转到登录页面.

~~~js
function checkLoginStatus(target, name, descriptor) {
  let oldFunc = descriptor.value;
  descriptor.value = function (...args) {
    let userInfo = wx.getStorageSync('userInfo');
    if (!userInfo) {
      wx.navigateTo({
        url: '/pages/login/login',
      })
    } else {
      oldFunc.apply(this, args)
    }
  }
  return descriptor;
}
~~~



下面是使用样例, 注意函数声明方式。
~~~js
// /pages/index/index.js
  @checkLoginStatus
  getUserInfo() {
      // ...
      // ...
  }
~~~

# 出错样例


下面的函数声明方式是不工作的, 可以正常编译运行，但是decorator不工作。
可以看到`let oldFunc = descriptor.value;`这个值是空的，也就是没有正确绑定目标函数`getUserInfo`.
~~~js
// /pages/index/index.js
  @checkLoginStatus
  getUserInfo: function() {
      // ...
      // ...
  }
~~~


#参考
1. [Segmentfault: JS 装饰器，一篇就够](https://segmentfault.com/a/1190000014495089)