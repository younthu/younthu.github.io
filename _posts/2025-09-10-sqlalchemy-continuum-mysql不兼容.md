---
layout: post
title: sqlalchemy continuum mysql不兼容
date: 2025-09-10 08:36 +0800
excerpt: "sqlalchemy continuum与mysql不兼容"
---

sqlalchemy continuum对数据库操作提供版本记录，但是对mysql不兼容，抛Sequence类型缺失问题，还有seq_id表不存在的问题。

最终放弃mysql, 把数据库换成psql，问题解决。

下面放一个psql版本下查询数据库修改记录的截图.
![alt text](/assets/img/sqlalchemycontinuum.png)


Psql有原生的audit支持，可以用pgAudit插件. 这个功能更加强大，但是查看日志比较麻烦一点。
