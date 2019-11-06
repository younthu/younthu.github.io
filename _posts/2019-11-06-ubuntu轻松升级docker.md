---
layout: post
title: ubuntu轻松升级docker
excerpt: 'ubuntu下面轻松升级docker到18'
category: '技术'
tags: 
- docker
- ubuntu
---

有台服务器17年搭的，docker版本有点老, 上面部署了很多容器, 好几次想升级都忍住了，怕把环境搞坏了有人找我麻烦。

这段时间总是出内存不足的问题，docker有个命令`docker stats`, 这个命令显示内存使用情况，但是老版本不显示container name. 网上说新版的支持显示container name了。忍不了，升级吧。

docker-ce和docker.io到底用哪个？ docker.io安装简单，我选docker.io

运行命令:
~~~
sudo apt install docker.io
~~~

一路回车，轻松升级成功！

手动启动被停掉的containers, easy!

docker升级还是非常简单的！