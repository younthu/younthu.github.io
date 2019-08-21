---
layout: post
title: anaconda jupyter notebook正确安装和使用opencv3
categories: ["技术"]
tags:
- python
- anaconda
- opencv3
- jupyter notebook
excerpt: "本文讲解mac下anaconda安装opencv3会遇到的问题和解决办法,保证jupyter下能正常使用opencv3."
---
本文主要解释安装python opencv3中常见的问题和解决办法，环境为python3 anaconda。

正常情况下我们anaconda安装好以后用pip装依赖包就好了，但是opencv3 在python3下面pip没法安装opencv3, 只能用conda安装。conda安装会引入很多细节上需要注意的问题。

[Anaconda opencv安装页面](https://anaconda.org/menpo/opencv3)

# anaconda安装
从[Anaconda下载页面](https://www.anaconda.com/distribution/#macos)下载 Command Line Installer.

然后从命令行安装。

安装完以后`zsh`下面你会大概率没法正常使用`jupyter notebook`, 需要修改~/.zshrc, 配置路径。

如果zsh无法正常使用`jupyter notebook`, 请在~/.zshrc末尾添加下面一行:
~~~sh
export PATH="/Users/<user_name>/anaconda/bin:$PATH" # 注意替换<user_name>为正确的用户名
~~~

# opencv在python3.6+下无法安装
现在默认anaconda装好后python版本是3.6+的, 用pip安装opencv3是没法安装的，可以用conda安装.

一般建议创建一个新的环境，然后安装opencv3
~~~
conda create -n p3 
source activate p3
~~~

安装menpo和opencv3
~~~sh
conda install -c menpo opencv3
~~~

会抛错！

~~~sh
$ conda install -c menpo opencv3
Error:
UnsatisfiableError: The following specifications were found to be in conflict:
  - opencv3 -> python 2.7*
  - python 3.6*
Use "conda info <package>" to see the dependencies for each package.
~~~

原因是python 3.5以上没法安装opencv3. 解决办法是安装一个python 3.5, 然后安装opencv3
~~~sh
conda install python=3.5
conda install -c menpo opencv3
~~~

安装成功，命令行验证
~~~sh
python -c "import cv2"
~~~

OK!

启动notebook验证
~~~
jupyter notebook
~~~

然后新建python3 文件, 输入`import cv2`, Shift+ENTER, 抛错，找不到cv2

~~~
import cv2;
---------------------------------------------------------------------------
ModuleNotFoundError                       Traceback (most recent call last)
<ipython-input-1-72fbbcfe2587> in <module>()
----> 1 import cv2

ModuleNotFoundError: No module named 'cv2'
~~~

查看查找路径会发现python指向的是3.6的路径。
~~~
import os
os.sys.path
~~~

不知道具体原因。解决办法是有的，从新创建一个基于python 3.5的anaconda环境,然后再装opencv3
~~~
conda create -n p35 python=3.5 anaconda
~~~

# 总结
anaconda+python3+jupyter notebook下面正确安装opencv3的步骤如下:

~~~
conda create -n p35 python=3.5 anaconda
source activate p35
conda install -c menpo opencv3
~~~

然后就可以在jupyter notebook里面正常使用opencv3了。