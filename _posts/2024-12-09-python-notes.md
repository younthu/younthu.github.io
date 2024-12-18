---
layout: post
title: python notes
date: 2024-12-09 13:45 +0800
excerpt: "一些python需要注意的坑"
---

1. 下面的代码是经常踩坑的:
    ~~~python
        for k,v in dict_obj:
            pass
    ~~~

    正确的写法:

    ~~~python
        for k,v in dict_obj:
            pass
    ~~~
1. global variable， 只读的时候不用声明，如果想在函数体里面赋值则必须声明，** 声明的语句必须放在函数体顶部 **。
1. Semaphore不能跨event loop使用，要不然会抛错`RuntimeError: <asyncio.locks.Semaphore object at 0x169de3f90 [locked]> is bound to a different event loop`，
1. 
    