---
layout: post
title: postgresql开放远程连接
date: 2021-01-26 18:10 +0800
excerpt: '出于安全原因考虑，Postgresql默认是不支持远程密码链接登录的。需要手动设置来允许远程连接，允许密码登陆.'
---

迁移一个老的爬虫项目，很多依赖很老了，环境不太好搭，搭折腾很长时间。爬虫搭起来以后，最后发现在数据库postgresql上面又踩了两个坑，还都是踩过的坑。

一个是postgresql中文编码的问题，默认编码是ascii编码, 导致爬虫写数据到数据库psql的时候中文内容写入失败.
这个问题有两种解决方案，一种是改连接字符串，一个是改psql的数据库设置，方法参见[postgres: UnicodeEncodeError ascii codec cant encode character \u2019](http://blog.ilibrary.me/2018/07/13/unicodeencodeerror-ascii-codec-can-t-encode-character-u2019-in-position).

这个解决以后呢，后面发现postgresql远程连接连不上，这跟以前也是踩过的, 忘记打卡远程链接导致的。

PC的这个远程连接还有点麻烦，有两步要设置，一步不够，这导致浪费了一些时间，所以记录下来。

# 解决方法

1. 第一步, 修改postgresql.conf，要在postgresql的下面允许接受来自任意IP的连接请求。 

  `postgresql.conf`存放位置在`/etc/postgresql/9.x/main`下，这里的x取决于你安装PostgreSQL的版本号，编辑或添加下面一行，使PostgreSQL可以接受来自任意IP的连接请求。
   ~~~
   listen_addresses = '*'
   ~~~
1. 第二步, 修改pg_hba.conf. 第一步改完以后呢还不够，他还得改pg_hba.conf，这文件允许密码认证方式。
   `pg_hba.conf`，位置与`postgresql.conf`相同，虽然上面配置允许任意地址连接PostgreSQL，但是这在pg中还不够，我们还需在pg_hba.conf中配置服务端允许的认证方式。任意编辑器打开该文件，编辑或添加下面一行。
   ~~~
   # TYPE  DATABASE  USER  CIDR-ADDRESS  METHOD
    host  all  all 0.0.0.0/0 md5    
   ~~~

   
默认pg只允许本机通过密码认证登录，修改为上面内容后即可以对任意IP访问进行密码验证。对照上面的注释可以很容易搞明白每列的含义，具体的支持项可以查阅文末参考引用。

完成上两项配置后执行sudo service postgresql restart重启PostgreSQL服务后，允许外网访问的配置就算生效了。

当然，记得打开防火墙上的端口.

# 参考
1. [如何设置PostgreSQL允许被远程访问](http://lazybios.com/2016/11/how-to-make-postgreSQL-can-be-accessed-from-remote-client/)