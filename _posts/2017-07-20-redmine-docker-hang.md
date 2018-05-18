---
layout: post
title: Redmine docker hang
---

** 2017.09.22 更新， 事实证明，host内存不足导致了容器的经常性hang, 加大内存问题就解决了**

# 强制删除容器

1. 强制删除容器。
~~~
docker rm container_id --force
~~~
该命令会先停止容器，再删除容器(如果没有删除成功，再用`docker rm <container_id>`做一遍). 但是！ 网络配置会删除不掉。


接下来运行`docker-compose -f stack.yml up -d`, 会看到类似下面的错误:

~~~
docker-compose -f stack.yml up -d
Creating redmine_redmine_1 ...
Creating redmine_redmine_1 ... error

ERROR: for redmine_redmine_1  Cannot start service redmine: endpoint with name redmine_redmine_1 already exists in network redmine_default

ERROR: for redmine  Cannot start service redmine: endpoint with name redmine_redmine_1 already exists in network redmine_default
ERROR: Encountered errors while bringing up the project.
~~~

解决办法请看第二步.

2. 删除网络配置

~~~
docker network ls
docker network inspect [network-name]
docker network disconnect -f  [Network-id] [Endpoint-Name]
docker network rm [network-name]
~~~

如果一遍不成，不要失去耐心，把容器(已经停止的容器)再删除一次,然后再`docker netork disconnect -f network-id endpoint-name && docker network rm network-name`

`docker network ls` 检查网络适配器没有了再重新`docker-compose -f stack.yml up -d`启动你的应用。

我最开始的时候network怎么也删不掉，过来一天再去删除轻松就删除了，不知道原因。

#Refs
1. [Hang when creating new issue](https://github.com/sameersbn/docker-redmine/issues/152)
1. [Docker container get stuck so that it can't be stopped or removed](https://github.com/moby/moby/issues/10589)
