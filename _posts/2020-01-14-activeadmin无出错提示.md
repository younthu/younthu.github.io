---
layout: post
title: activeadmin无出错提示
excerpt: "Active Admin创建对象失败的情况下没有出错提示. 解决办法是加object_form.semantic_errors :base"
category:
- 技术
tags:
- Active Admin

---
Active Admin创建对象失败的情况下没有出错提示, 页面继续留在新建对象页面. 
原因是自定义了form表单，把validation提示弄丢了。

解决办法是加`object_form.semantic_errors :base`。

具体参考下面的代码，把带注释的那一行加上就可以了。

~~~
form do |f|
  object_form.semantic_errors :base #  这一行用来显示出错提示
  f.has_many :object do |object_form|
    
    object_form.input :color
  end

end
~~~


参考
1. [Form DSL support for formtastic's semantic_errors method](https://github.com/activeadmin/activeadmin/pull/905#issuecomment-15080389)

