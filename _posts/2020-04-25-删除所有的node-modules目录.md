---
layout: post
title: 删除所有的node_modules目录
category: 技术
tags: 
- find
- 命令行
- 磁盘清理

excerpt: '删除所有的`node_modules`目录, 目录遍历深度为4: `find . -type d -name node_modules -d 4 -exec rm -rf '{}' \;`
---
node_modules有点bug，占空间，还不在一个集中的地方, 项目多了的时候清理起来特别麻烦。

这里有一个一次性清理所有`node_modules`的命令行, 这命令行查找当前目录下深度为4的所有子目录里的`node_modules`文件夹, 去掉`-d 4`就可以取消深度限制，遍历所有子目录:

~~~shell
find . -type d -name node_modules -d 4 -exec rm -rf '{}' \;
~~~
