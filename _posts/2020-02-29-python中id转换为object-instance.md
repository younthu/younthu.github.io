---
layout: post
title: python中id转换为object instance
category: 技术
tags:
- Python
- ctypes
---

# functools.lru_cache
python有个内置的functools.lru_cache, 这是一个基于lru的cache，作用于函数或者类方法上. 该装饰器会根据参数值来缓存方法调用的结果。

但是，被装饰的方法的参数必须是hashable的。如果是强类型的, 改类型需要实现`__hash__()`方法.   

基础类型(int, long, float, bool, )，方法, string, tuple, 都是hashable的. 

下面的代码是合法的。 
~~~python
t = (3,2,"aaa",)

e = {d: "a", 
dir: 1, 
"str": 3, 
str: 4
}
~~~

可以通过下面的代码获取内置类型

~~~python
dir(globals()['__builtins__'])
~~~



如果传了不可hashable的参数，那么该装饰器会不工作。

一个解决办法是传递对象的`id(object)`。


# Ctypes.cast


~~~python
import ctypes
a = "hello world"
print(ctypes.cast(id(a), ctypes.py_object).value) # 注意ctypes.cast返回的是一个py_object, py_object.value指向原始的对象.
~~~



