---
layout: post
title: rails dump/restore数据
date: 2021-04-15 14:34 +0800
excerpt: '可以通过gem直接dump/restore数据库，省得去记各种数据的dump，restore命令。缺点就是速度非常慢。rails就是慢。好处也非常明显，可以跨数据库。'
---

可以通过gem [yaml_db](https://github.com/yamldb/yaml_db)直接dump/restore数据库，省得去记各种数据的dump，restore命令。

确定就是速度非常慢。rails就是慢。 数据库太大就不合适了。

有点就是能跨数据库迁移数据。比如sqlite3到postgres,到mysql的迁移。因为yaml_db用了yaml来保存数据，不是sql脚本，屏蔽了数据库之间脚本的差异。

方法:

1. 添加`gem 'yaml_db'`到`Gemfile`
2. bundle install
3. `rails db:data:dump` #  Dump contents of Rails database to db/data.yml
1. `rails db:data:load` # Load contents of db/data.yml into the database
1. 在服务器上dump的时候没有任何压力
1. 在服务器上load的时候内存爆了。dump文件4.7G, 服务器内存8G。后来转开发机load, 开发机32G内存. 在开发机上，最高消耗14.1G-
2. 在开发机上load到psql里面去，任何再用psql到处备份文件，再恢复到服务器的psql上去。
1. yaml_db不支持外键， 遇到下面的问题: 
   ~~~
   ActiveRecord::StatementInvalid: PG::FeatureNotSupported: ERROR:  cannot truncate a table referenced in a foreign key constraint
    DETAIL:  Table "user_comments" references "rainbows".
    HINT:  Truncate table "user_comments" at the same time, or use TRUNCATE ... CASCADE.
   ~~~
   参考: 
   1. https://stackoverflow.com/questions/37363637/exporting-database-from-development-to-production
   1. 

最后还是sqlite dump sql scripts, 手动改sql scripts, 再倒入到psql里面去
1. 导出sqlite数据: ` sqlite3 rainbow_production.sqlite3 .dump > dump.sql`
1. 导出schema: `sqlite3 rainbow_production.sqlite3 .schema > schema.sql`, 这个schema根据自己的实际情况选择用还是不用.
1. put `\set ON_ERROR_STOP on` into `~/.psqlrc`, 有了这个配置以后psql导入数据出错后就会里面停下来，看到错误信息。
1. 连接psql: `psql -U postgres -h db -d rainbow`
1. 清除ar_internal_metadata里的内容`delete from ar_internal_metadata`
1. sql scripts清理:
    1. 清空rainbows: `delete from rainbows`
    1. 删除第一行: `sed -i '' -e '1d' data.sql.bak`
    1. 删除data.sql.bak里面末尾sqlite_sequence相关的数据: `sed -i '' '/sqlite_sequence/d' data.sql.bak`
1. 导入数据到psql: `psql -d rainbowfart_dev -U postgres < data.sql.bak`


