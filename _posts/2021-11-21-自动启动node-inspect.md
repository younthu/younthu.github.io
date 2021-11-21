---
layout: post
title: 自动启动node inspect
date: 2021-11-21 10:30 +0800
---
代码演示在nodejs项目怎样启动一个类似console的东西, 同时自动启动node inspect.

1. console.js
   ~~~js
   	#! /usr/bin/env node --inspect
	var repl = require('repl')
	Client = require('./Client').Client

	repl.start()
   ~~~
   这个console会启动一个repl, 同时，导出了一个类`Client`。

   可以在repl里面直接使用`Client`, 也可以在chrome node inspect里面直接使用`Client`.

   代码第一行 `#! /usr/bin/env node --inspect` 表示默认启动chrome node inspect.
2. TCPClient.js
   ~~~js
   function Client() {
	   this.sayHello = function(){
		   console.log("hello")
	   }
   }
   	module.exports = {
      Client
	}
   ~~~
3. 在package.json里面加入下面语句:
   ~~~json
     "scripts": {
    	"tcp_client": "./console.js",
	}
   ~~~
4. 启动console: `yarn console`.
   1. 这会启动一个repl, 保存代码不退出，
   2. 同时启动chrome node inspect, 开发者可以在node inspect里面去调试代码.
5. 可以在chrome node inspect里面调试代码了。 尝试输入: `client = new Client(); client.sayHello()`
