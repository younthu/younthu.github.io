---
layout: post
title: kaminari翻页count慢的解决办法
categories: Rails
tags:
- Rails
- Kaminari
- Cache
excerpt: '用kaminari做分页，每次翻页前都会select count(*)来统计总记录数，当数据库记录很大的时候非常慢，解决办法就是override total_count(),再用cache.'
---

# 解决Kaminari分页count大量数据慢的问题

[Kaminari](https://github.com/kaminari/kaminari)做翻页的时候,每次翻页前都会select count(*)来统计总记录数，当数据库记录很大的时候非常慢。

如果数据库的记录数量不是经常变化, 那可以通过cache的机制来解决。 Kaminari自身不支持cache count, 需要通过overwrite total_count方法来做.

下面是在controller里面overrite scope total_count来做count cache的实现，在dev模式下可以通过rails dev:cache来开启cache查看效果。如果要关闭dev cache, 再次运行`rails dev:cache`或者删除 tmp/caching-dev.txt即可。

~~~ruby
def index

    a = User.all
    a.instance_eval do
      def total_count
        Rails.cache.fetch("users_total_count", expires_in: 240.hours) do
          User.count
        end
      end
    end
    @users = a.page(params[:page]).per(25)
  end
~~~

该方法成功让分页的数据库查询开销从20s降低到1ms.

该方法也有其局限性:
1. 不能通用。每个scope都得自己做cache，要不然就会出错。
2. 如果有新的记录加入，这个count会出错了。
3. 翻到最后的页的时候还是会慢。

# 参考
1. [Caching in development environment in Rails 5](https://blog.bigbinary.com/2016/01/25/caching-in-development-environment-in-rails5.html)