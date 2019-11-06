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

下面是升级后`docker stats`的输出, 可以看到`NAME`列了:

~~~sh
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
592992b1b85e        yxxx                0.01%               103.6MiB / 1.953GiB   5.18%               25.2kB / 74.3kB     68.4MB / 53.2kB     13
6d2a0fd3acc6        yuexxxxxxxxin       0.00%               124.1MiB / 1.953GiB   6.20%               42.9kB / 586kB      55.6MB / 4.1kB      13
42a51839b4b8        lixxxxxxx-python3   0.02%               72.09MiB / 1.953GiB   3.60%               153kB / 22.9MB      48.1MB / 0B         4
787789d7f03f        joxxxxstatic        0.00%               10.07MiB / 1.953GiB   0.50%               883kB / 1.8MB       11.2MB / 0B         2
~~~

docker升级还是非常简单的！