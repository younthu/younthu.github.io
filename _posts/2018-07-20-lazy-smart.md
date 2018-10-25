---
layout: post
title: 懒人笔记
---
懒人笔记

批量下载递增序列图片

~~~
curl http://example.com/pic[1-24].jpg
~~~

批量删除指定名称的目录

~~~
find . -type d -name 'node_modules' -exec rm -r {} + # 删除之前你可以先用 find . -type d -name 'node_modules' 把所有目录列出来检查一遍
find . -type d -name 'build' -exec rm -r {} + #删除所有build目录
~~~

命令行日期管理

~~~
calcurse
~~~
