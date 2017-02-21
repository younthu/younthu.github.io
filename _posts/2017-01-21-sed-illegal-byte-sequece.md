---
layout: post
title: sed - illegal byte sequece
---
# sed处理utf-8中文字符的时候抛错
有一个文本文件，utf-8编码的，想在每行用双引号包起来, 于是用sed处理.

~~~shell
sed 's/^/\"/g' forbiddenwords.txt | sed 's/$/\":\"\"/g' > forbiddenwords2.txt
~~~

结果抛下面的错:

~~~shell
sed: RE error: illegal byte sequence
~~~



本机shell 环境:

~~~shell
➜  ~ locale
LANG=
LC_COLLATE="C"
LC_CTYPE="UTF-8"
LC_MESSAGES="C"
LC_MONETARY="C"
LC_NUMERIC="C"
LC_TIME="C"
LC_ALL=
➜  ~ uname -a
Darwin XXXX-MacBook-Pro.local 16.3.0 Darwin Kernel Version 16.3.0: Thu Nov 17 20:23:58 PST 2016; root:xnu-3789.31.2~1/RELEASE_X86_64 x86_64
~~~

# 解决办法

这是内容编码的问题，在命令行里面设置编码语言就可以了
~~~shell
export LANG='UTF-8'
~~~

永久解决这个问题的办法是在shell(~/.zshrc)配置文件里面加入下面语句

~~~shell
export LANG='UTF-8'
~~~

这可能会导致vim显示utf-8乱码,在~/.vimrc里面加入以下语句可以解决问题:

~~~shell
set encoding=utf-8
~~~