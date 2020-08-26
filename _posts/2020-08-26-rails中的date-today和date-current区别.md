---
layout: post
title: rails中的Date.today和Date.current区别
date: 2020-08-26 07:56 +0800
excerpt: Date.today是UTC时间, Date.current是locale时间, Time.current也是locale时间, 1.days.after也是locale时间。所以不要用Date.today, 用另外3种形式.
category:
- 技术
tags:
- ruby
- Date
---
Date.today是UTC时间, Date.current是locale时间, Time.current也是locale时间, 1.days.after也是locale时间。所以不要用Date.today, 用另外3种形式. 也不要用Time.now.

在console里面执行`$ Date.current`, 可以看到如下代码:

~~~ruby
def current
  ::Time.zone ? ::Time.zone.today : ::Date.today
end
~~~

Date.current会判断时区，然后计算好时区以后返回当地时间。

在console里面执行`$ 1.days.later`, 可以看到如下代码:

~~~ruby
def since(time = ::Time.current)
  sum(1, time)
end
~~~

这个也是基于时区来计算的，大部分情况下我们需要的就是基于时区的时间。

注: 美元符查看源码`$ Date.current`是`pry`的功能, 需要安装`gem pry`之后才能正常使用。

# 总结

大部分情况下我们应该用`Date.current`, `1.days.after`和`Time.current`，不要用`Date.today`, 也不要用`Time.now`.

Rails里面没有`Date.now`.