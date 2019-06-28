---
layout: post
title: elasticsearch docker crash
excerpt: es比较喜欢因为磁盘问题而挂掉。
---

最近几天频繁发生elasticsearch挂掉的事情。虽然以前也发生过因为内存不足导致es挂掉，但是没有这么频繁。

近期服务器也没有做什么特殊的改动，ES里面只增加了大概3万多条金融机构数据，3万条记录和es里现有的记录数相比应该是小数，不至于造成内存问题。


怀疑
1. 内存不足

## 分析docker log

`docker logs xxxxxx`

内容太多，可能几个月前的日志都会给你打印出来，网速不好的情况下得花上几个小时才能打印完了. 可以加一个`--tail`参数来仅打印最后的多少条日志。

`docker logs --tail 1000 xxxxxx`, 仅打印最后的1000条日志.


发现下面日志:
~~~
[o.e.c.r.a.DiskThresholdMonitor] [fbSSlo4] high disk watermark [90%] exceeded on [fbSSlo4pReKM3jIFdbnZfA][fbSSlo4][/usr/share/elasticsearch/data/nodes/0] free: 11.6gb[5.9%], shards will be relocated away from this node
~~~

从这日志来看怀疑是磁盘空间不够导致es挂掉了。

删除日志文件和数据库备份，问题解决。

## 解决ES 5.x xpack license过期的问题

https://medium.com/@ospaarmann/tidbits-solving-the-elasticsearch-x-pack-license-issue-in-docker-d15bb22d82fd
