---
layout: post
title: scrapy + postgres: UnicodeEncodeError ascii codec cant encode character \u2019 
exceprt: scrapy爬虫,python3, postgres做存储，在Mac上能正常工作，在ubuntu docker上抛错: UnicodeEncodeError: 'ascii' codec can't encode character '\u2019' in position
---

Scrapy爬虫在Mac上能正常工作，在ubuntu docker上跑的时候出问题，抛下面的错误:

~~~
UnicodeEncodeError: 'ascii' codec can't encode character '\u2019' in position
~~~

用的是Python3(Anaconda), scrapy, postgresql.


开始在python端搞了半天没有结果，python的encoding都是正常的。

~~~
import sys
sys.getdefaultencoding() # utf-8
~~~


后来检查postgresql编码，发现问题出在数据库编码上

~~~
psql -l

   Name     |  Owner   | Encoding  | Collate | Ctype |    Access privileges
-------------+----------+-----------+---------+-------+--------------------------
 getnews_dev | postgres | SQL_ASCII      | C       | C     | =Tc/postgres            +
             |          |           |         
~~~


修改方法:

在psql里面修改数据库编码

~~~
postgres@xxxxdd:/psql

postgres=#update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'getnews_dev'
~~~

问题解决!
