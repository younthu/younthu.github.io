---
layout: post
title: webpack integrity check failed
category: 
- 技术
tags:
- Rails
- Webpack
excerpt: " Your Yarn packages are out of date!
  Please run `yarn install --check-files` to update"
---

# webpack integrity check failed

新搭的rails docker启动`rails s`的时候抛下面错误:

~~~
warning Integrity check: System parameters don't match
error Integrity check failed
error Found 1 errors.


========================================
  Your Yarn packages are out of date!
  Please run `yarn install --check-files` to update.
========================================


To disable this check, please change `check_yarn_integrity`
to `false` in your webpacker config file (config/webpacker.yml).


yarn check v1.19.1
info Visit https://yarnpkg.com/en/docs/cli/check for documentation about this command.
~~~

# 解决办法

删除 `yarn.lock` 文件

# 参考
1. [Railtie yarn check --integrity fails on development](https://github.com/rails/webpacker/issues/1374#issuecomment-541603865)