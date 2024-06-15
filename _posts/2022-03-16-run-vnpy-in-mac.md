---
layout: post
title: run vnpy in mac
date: 2022-03-16 18:59 +0800
excerpt: '在Mac中跑vnpy'
---

参考https://www.vnpy.com/docs/cn/ubuntu_install.html. 整个过程很suffer，最终放弃了。

1. `conda create --name vnpy_python3.7 python=3.7`
2. `conda activate vnpy_python3.7`
3. `git clone  https://github.com/vnpy/vnpy.git`
4. `cd vnpy`
5. `brew install ta-lib`
6. `./install_osx.sh`
7. `python run.py`
8. 抛错
   ~~~sh
	Traceback (most recent call last):
	File "run.py", line 5, in <module>
		from vnpy.trader.ui import MainWindow, create_qapp
	File "/Users/i532140/sourcecode/vnpy/vnpy/trader/ui/__init__.py", line 1, in <module>
		from .qt import QtCore, QtWidgets, QtGui, Qt, create_qapp
	File "/Users/i532140/sourcecode/vnpy/vnpy/trader/ui/qt.py", line 9, in <module>
		from PySide6 import QtGui, QtWidgets, QtCore
	ImportError: could not import module 'PySide6.QtCore'
   ~~~
   解决办法:
8. `python examples/no_ui/run.py`,  抛错
   ~~~sh
    Traceback (most recent call last):
	File "examples/no_ui/run.py", line 11, in <module>
		from vnpy_ctp import CtpGateway
	ModuleNotFoundError: No module named 'vnpy_ctp'
   ~~~
   解决办法:
    1. 注释掉`examples/no_ui/run.py`里面ctp和cta相关的代码.
    2. 命令行可以起来，不知道怎么用.

9.  `python examples/vn_trader/run.py`, 抛错:
	~~~sh
		Traceback (most recent call last):
		File "examples/vn_trader/run.py", line 5, in <module>
			from vnpy.trader.ui import MainWindow, create_qapp
		File "/Users/i532140/opt/anaconda3/envs/vnpy_python3.7/lib/python3.7/site-packages/vnpy/trader/ui/__init__.py", line 1, in <module>
			from .qt import QtCore, QtWidgets, QtGui, Qt, create_qapp
		File "/Users/i532140/opt/anaconda3/envs/vnpy_python3.7/lib/python3.7/site-packages/vnpy/trader/ui/qt.py", line 9, in <module>
			from PySide6 import QtGui, QtWidgets, QtCore
		ImportError: could not import module 'PySide6.QtCore'
	~~~
10.
