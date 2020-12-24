---
layout: post
title: mac float window on the top
date: 2020-12-23 14:56 +0800
excerpt: 关于怎样屏蔽mac微信公众号的推送,换你一个清静的世界.
---
我发这篇博客主要是为了告诉大家怎么屏蔽掉mac电脑微信上的官方订阅号推送。

那个官方订阅号推送实在是太让人难受了，不停地推送文章，打断正常的工作。

下面就是屏蔽公众号推送影响的方法，没有真正删除那个推送，只是把推送窗口沉底了，沉到看不见的地方了。
世界从此清静！

# 关闭微信公众号推送
Wechat Extension里面有一个sticky bottom的功能, 可以让公众号的推送一直沉底。

# refs
1. [SIMBL插件模式](https://www.maketecheasier.com/mac-keeping-your-application-window-always-on-top/) 
    1. [github MacForge](https://github.com/MacEnhance/MacForge), mac plugin manager
    1. [Afloat github](https://github.com/rwu823/afloat), always on top plugin, 需要MacForge支持。
    
1. [Helium](https://heliumfloats.com/), 一个置顶视频播放的浏览器.
    1. 开源，MIT, [Helium](https://github.com/JadenGeller/Helium)
1. [picture-in-picture-chrome-extension](https://github.com/GoogleChromeLabs/picture-in-picture-chrome-extension)
    1. google出品，chrome插件, Apache
1. [Wechat extension](https://github.com/MustangYM/WeChatExtension-ForMac), 微信插件, 开源MIT.
    1. 核心代码来自于[TKkk-iOSer](https://github.com/TKkk-iOSer/WeChatPlugin-MacOS/tree/master)
    1. TKkk还有iOS的插件[TKkk WeChatPlugin-iOS](https://github.com/TKkk-iOSer/WeChatPlugin-iOS/tree/master)
    1. 这里面有一个sticky bottom的功能，让微信公众号推送永远沉底。
1. 
