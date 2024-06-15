---
layout: post
title: visual studio code 二次开发
date: 2021-09-03 23:50 +0800
---
做了一个vs插件[VS encrypt text](https://github.com/younthu/vs-encrypt-text), 做明文加密解密的。

主要是用于加密文本文件里面的明文密码或者敏感信息。在VS Code里面选中指定的文本就可以加密/解密选中的内容。

没有正式上架VS extension market.

有机会还是得把这个上架一下，应该还是有需求, 我个人就非常需要这种功能。

# 插件开发
1. `npm install -g yo generator-code`


# refs
1. [vscode二次开发_认识vscode(一)](https://blog.csdn.net/weixin_39571087/article/details/111670815)
2. [vscode技术揭秘](https://segmentfault.com/a/1190000020833042)
3. [搭建项目开发工具IDE：如何编写 VSCode 插件](https://mp.weixin.qq.com/s/Opn9MjKXNxXZ0L15EPIvug),
4. [vscode 定制开发](https://zhaomenghuan.js.org/blog/vscode-custom-development-basic-preparation.html), 电子书
