---
layout: post
title: Rails中用Sqlite的坑
categories:
- 技术

tags:
- Sqlite
- Rails
- Docker

---

前端时间把一个小应用移到docker里面去了。真的是吐血！搞了好几个周末才搞定。

这是一个rails应用，提供一套api，加一些简单的页面。因为应用非常简单，没有数据量，也没有用户，这种东西最适合放docker里面去了。为了方便下次的数据迁移，选择了Sqlite3。以前应用是直接部署在host里，用mysql, 这几天服务器借给朋友用，搞坏了，数据不好恢复，也懒得恢复，一咬牙就直接上了docker + sqlite3这套组合,docker image是`ubuntu 16.04`, `rails 5`, `nginx`, `passenger`, `capistrano`。

漫漫填坑路由此开始...

## Read-only database & 无日志
docker搭好以后服务器貌似一切正常，后台`rake db:seed`导入种子数据，刷新页面，也OK。 于是高高兴兴睡觉去了。

第二天尝试更新数据，发现数据更新不上。我想是不是服务器环境搭建哪里出问题了，于是删了重新搭建，问题依旧，反复好几次，无解。还发现服务器Rails日志也不对，总是只有最开始的操作有日志。

确认自己的搭建步骤没有问题以后开始认真查看日志。首先发现Rails的错误日志被写入到nginx日志里面去了。里面有下面的错误记录:

`FATAL -- : ActiveRecord::StatementInvalid (SQLite3::ReadOnlyException: attempt to write a readonly database`

Google以后找到解决方案: nginx的用户权限不够，修改nginx的用户为root，日志出来了，问题解决。解决方法就是把`/etc/nginx/nginx.conf`  第一行用户

可是，数据库还是写不进去。 改文件访问权限也没用。后来无意中`ps -aux`命令发现passenger用的是一个独立的用户，于是修改passenger user 为root. 数据终于可以写进去了。 passenger 的配置文件在`/etc/nginx/nginx.conf`中有指定，查找passenger配置文件`include /etc/nginx/passenger.conf`

也许这个锅不该全甩给Sqlite, 但是，如果是用mysql,或者postgresql, 这个文件层面的访问权限问题就不会有了对吧。这个锅还是得sqlite背。

中途也想放弃sqlite, 换回mysql, 可是一想数据迁移的事情，哎，忍了！坚持修复了。

复盘一下问题:

1. 日志不能写是因为nginx启动权限不够。我把日志文件加了`chmod 777 -R log`, 没有效果，不知道原因。
2. 种子数据能写入数据库是因为我用root登录，然后用rake导入，root权限，写入无障碍。rake生成的日志也会写入log. 这也是为何能看到有限的日志的原因。
3. 日志能写入以后数据还是不能写。这是因为passenger用的又是另外一个用户，它能读sqlite问题，但是不能写入。所以数据导入以后页面能正常显示，一旦写入就崩溃了。

## True or 't', False or 'f'
事情还没有完，如果只是权限的问题，我肯定就算了。也怪我心大，数据库能写入以后又去睡觉了。第二个周末一看，所有的bool逻辑都不对啊。去后台一查，bool值全部是't','f'.  WTF!

尝试了打补丁，这里是[补丁代码](https://gist.github.com/ajoman/9391708), 这里是[stackoverflow 讨论](https://stackoverflow.com/a/27613537) . 都不管用。

来来回回折腾了好几次，无解。

跟坑人的事情是它在mac上工作好好的，在ubuntu上面就不工作了。坑爹吧！

再次想切换到mysql，搞了几次发现docker composer不熟，要起几个docker container难度有点大，以后迁移还是痛苦。忍！

后来发现redmine的代码能很好的在sqlite上工作，比对了一下migration脚本，发现redmine的脚本里面对boolean的默认值有做设置。
我的语句长这样`t.boolean `, redmine的长这样`t.boolean :is_even,    default: true,      null: false`。 它多了`default: true, null: false`. 于是依葫芦画瓢添上。我也不知道是不是这几句起作用了，反正现在能工作了。



## rename_column失败
这个我都不想说了，这个其实是最早做migration的时候遇到的一个奇葩问题：
`sqlite不能很好支持migrate的rename_column`.

刚开始用load:schema来跳过这个问题。可是因为日志和数据库写入的问题，环境被反反复复搭建，每次都得手动load:schema，恐怖程度可想而知。


挣扎了很久以后把renam_column的语句删了. 人生就是这样，有时候遇到这种情况只能对自己下狠手。不然你还想怎样？:(

这几个问题前前后后花了我接近一个月(周末).

# 总结
Sqlite坑多，慎入!

那个rename column的问题至今未解决。
