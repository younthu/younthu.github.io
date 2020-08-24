---
layout: post
title: 通过ssh forward远程部署代码
date: 2020-08-24 21:10 +0800
excerpt: '通过ssh forward, 本地ssh private不用部署到远程服务器，可以让远程服务器使用本地ssh key访问受限资源. 省却ssh key到处部署，管理麻烦到问题。'
---

# 设置ssh forward

在~/.ssh/config里面设置如下内容即可开启ssh forward
~~~sh
# ~/.ssh/config
Host *
  AddKeysToAgent yes
  ForwardAgent yes
  IdentityFile ~/.ssh/id_rsa
~~~

可以通过下面的命令测试ssh forward agent是否生效:

~~~sh
ssh -T git@github.com # -T表示测试
~~~

或者打印日志

~~~sh
ssh -T -v git@github.com 
~~~

# 通过ssh agent来git clone

通过ssh远程到目标服务器以后就可以通过git命令来克隆git repository了。
注意clone路径必须是.git形式`git clone git@github.com:<user>/xxxxx.git`, 有准确部署deploy key.


不仅`git clone`可以用ssh agent来做。所有其它ssh的操作都可以通过ssh agent forward来做代理授权，非常方便。再也不用把自己的私钥到处拷贝来拷贝去了。 