---
layout: post
title: 懒人笔记
excerpt: 本文主要分享一下个人总结的工作生活管理方法。工作生活中最重要的两件事情是时间管理和知识管理。做为一天到晚呆电脑前面的我，自己总结了一套管理方法和配套工具,核心观点就是所有的工具都往命令行靠，尽量不用图形化界面，尽量不要开网页。
---

# 时间管理
时间管理其实也是任务管理。我尝试过很多种工具去管理我的时间，我的任务，效果都不是很好。试过记事本，纸质笔记本，onenote, 各种task manager, redmine等等，刚开始觉得很有用，用着用着就不用了。
最后发现对于常年呆电脑前面的我，命令行工具是最方便，最适合我的。

我现在时间管理基本上就用命令行工具[task warrior](https://taskwarrior.org/), 该工具有插件接口，很多免费的插件可以用。我用了一个免费的在线同步插件[taskwarrior.inthe.am](taskwarrior.inthe.am), 有了这个插件就可以在不同电脑之间同步任务了。

这个工具简单，没法和redmine,jira等工具相比，但是我日常的工作够用了。如果涉及到大团队合作那task warrior是没法支持的。

另外一个我常用的命令行工具是`cal`, 查看日历。比用鼠标点来点去方便很多。

# 知识管理
对于知识管理，我写过日志，太难坚持了，因为你要发布给别人看的话要求就高了。也用过evernote, 用着用着就放弃了，原因在于查看不方便，并且复制黏贴得越多到后面就越不想看。
可视化只是管理的一个通用弱点是格式化负担太重了，想把文本只是内容整理得漂亮需要很多编辑排版的经验，也非常耗时间，很多人格式化做着做着就放弃了，包括我。

现在我知识管理用`cheat`比较多，[cheat github](https://github.com/cheat/cheat)。cheat有一些内置的帮助文档，内容很少，这是远远不够的。我的做法是在工作中遇到一些比较麻烦或者生僻不好记的知识点的时候就自己用`cheat`的编辑功能记录下来，大概率你会回头查看你以前用过的一些命令，一些代码，这个时候就可以用`cheat`来回顾了。cheat的好处是足够简洁，使用方便，命令行直接出结果。同时，cheat还支持简单的搜索`cheat -s your_key_word`.

如何保持cheat自己编辑的内容？

你可以通过软链接把'~/.cheat'目录链接到一个git repository 下面去，然后就可以用github管理同步你的知识点啦。

cheat主要还是适用于一些简单零散的知识管理。对比较系统化的知识管理，还是应该考虑写日志。写日志是对知识的整理回顾，写的同时会帮你梳理很多以前遗漏的东西，可以加强你对知识的理解。同时写日志可以很好的锻炼表达能力，这一点非常重要。

写日志太耗时间了，建议不要急于求成，可以先挖一个坑，然后慢慢填。

写日志我用`jekyll`, 然后选一个模板，用markdown写，然后免费发布到github上。

# 常用命令行工具

现在对命令行依赖越来越重。现列一下我常用的命令行工具

命令行工具 | 解释
---------|--------
vim | 文本编辑
iterm2 | 一个增强的term
zsh | 一个增强的shell
find | 文件查找


<未完待续>
