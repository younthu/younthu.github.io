---
layout: post
title: Cline开发- Hello World
date: 2025-09-25 22:58 +0800
tags: [cline]
excerpt: "修改Cline之Hello World, 让它喊爸爸"
---

Cline是一个开源AI编程工具，支持以插件形式安装到Visual Studio Code, 非常有名。

![cline screenshot](/assets/img/Cline/demo.gif)

既然开源，那肯定就可以拿它的代码改了。Cline是用js写的，这个我熟。

但是对Cline的代码不熟，怎么下手？

进入到陌生领域的时候，千万别忘记编程界的万用法宝- Hello World.

我想改Cline, 不知道从哪下手，还是写个Hello World吧。 

注意，这是教你怎么改Cline, 不是怎么使用Cline. 

# 目标

在开场的时候，让Cline喊我爸爸.
![alt text](/assets/img/Cline/cline_daddy.png)

# 步骤

1. 搭建开发环境
2. 修改提示语

# 环境搭建

根据[Contribution Guide](https://github.com/cline/cline/blob/main/CONTRIBUTING.md) 搭建环境.

1. `git clone https://github.com/cline/cline.git`
2. `yarn install:all`
3. `code .`
4. 搜索`what can i do for you?`, 修改成`what can i do for you, 爸爸?`, 保存.
5. `Run` -> `Start Debugging`, 启动visual studio code 调试instance.
6. 在调试模式下点击Cline, 可以看到`Waht can i do for you, 爸爸?`.
   ![alt text](/assets/img/Cline/hello_daddy.png)

 有点出乎意料地简单了。下次整个难一点的。

 # 总结

 1. Cline的Greetings没有做本地化， 是写死在js文件里的。
 2. 修改和调试Cline很容易, 调试Visual Studio Code插件很简单，不需要反锁的插件安装步骤。