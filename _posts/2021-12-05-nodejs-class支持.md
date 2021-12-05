---
layout: post
title: nodejs class支持
date: 2021-12-05 15:39 +0800
---
nodejs 13.2之后支持ES6 class语法, 支持类的定义，支持`import`语法。有两种方法可以打开这种语法支持:

1. 在package.json文件的顶层加入下面`"type":"module"`. 这种模式下所有js文件都会被已ES modules的格式, `require`不能用,如果有比较多的js包含`require`则改起来麻烦，不方便。
   ~~~json
    // package.json
	{
	"type": "module"
	}
   ~~~
2. 用文件后缀`.mjs`显示指明该文件使用ES modules, 支持`export default class XXXX`和`import XXXXX from 'xxxx`。`.js`文件会被解析成Common JS格式。

老项目推荐用第二种方法。新项目推荐用第一种方法。
# ref
1. [SyntaxError: Cannot use import statement outside a module](https://stackoverflow.com/a/59399717)
