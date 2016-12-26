---
layout: post
title: React Native系列 - disable HTTP2

categories:
- React Native

tags:
- React Native
- PR

excerpt: 发了一个PR给facebook, 主要是提供了一个接口供遇到同样问题的用户去绕过这个问题.在该PR被接受/拒绝之前大家可以用我的私有版本来解决这个问题，详见另外一篇文章。
---

# 发了个PR
发了一个[PR](https://github.com/facebook/react-native/pull/11372)给facebook, 主要是提供了一个接口供遇到同样问题的用户去绕过这个问题。

# 至今未被合并

因为那段时间真不巧，等我第一次review结束后，连续几天react-native的master分支总是build failure, 等过了好几天终于不再build failure了，我再次提醒他们合并的时候，再也没有人鸟我了。:(

维护开源项目部容易，要精力，也要耐心，还要有奉献精神。

如果有人需要绕过这个问题，请参考我的另外一篇文章[okhttp中的坑](2016-11-30-okhttp中的坑)
