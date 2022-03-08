---
layout: post
title: activeadmin后台filter性能问题
date: 2022-01-29 16:34 +0800
excerpt: 'activeadmin后台有个页面特别慢，大概要30s。一开始以为是sql问题，后来发现是activeadmin渲染的问题'
---

1. 3w条log
2. 1千多条issue.
3. log和issue是多对一关系。
4. issue页面本地渲染要30多秒。
5. 把filter去掉以后300ms渲染完成.
