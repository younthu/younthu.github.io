---
layout: post
title: c++repl jupyter notebook
date: 2021-08-20 22:15 +0800
excerpt: "用jupyter notebook + cling 做C/C++的REPL环境"
---
C++非常不方便的一个地方就是没有官方成熟的REPL。

现在有一个开源项目[Cling](https://github.com/root-project/cling),The cling C++ interpreter.

下面是官方介绍:

Cling is an interactive C++ interpreter, built on top of Clang and LLVM compiler infrastructure. Cling realizes the read-eval-print loop (REPL) concept, in order to leverage rapid application development. Implemented as a small extension to LLVM and Clang, the interpreter reuses their strengths such as the praised concise and expressive compiler diagnostics.

并且，这个cling自带一个 [jupyter](http://jupyter.org/) kernel. 并且有[Jupyter Cling安装教程](https://github.com/root-project/cling/tree/master/tools/Jupyter).

conda上也有cling的包，可以通过`conda install -c conda-forge xeus-cling` 安装， 这里有[安装说明](https://xeus-cling.readthedocs.io/en/latest/installation.html#using-the-conda-forge-package)。

我在Mac上安装, 启动后kernel不能正常工作，跑C++代码的时候抛错:

~~~
error: no member named 'strtod' in the global namespace
using ::strtod;

error: no member named 'atoll' in the global namespace
~~~

也试过几个docker, 都有各种问题，大部分都用不了，只有一个能正常跑代码。
1. [Docker-Jupyter-Xeus-Cling-OpenCV](https://github.com/Seachaos/docker-python-xeus-cling), 不能正常工作. known issue: cpp17 may not working.
   1. [Seachaos/opencv-cpp-for-xeus-cling](https://github.com/Seachaos/opencv-cpp-for-xeus-cling)
2. [xeus-cling C++ Jupyter kernel inside a docker container](https://gist.github.com/dsuess/059b86ea55d639bb99175c9a8cd2ca3e), 不工作。base image换成continuumio/miniconda3还是不行。
3. [xeus cling官方](https://xeus-cling.readthedocs.io/en/latest/installation.html#using-the-conda-forge-package), 也不管用。
1. [Xeus-Cling: Run C++ code in Jupyter Notebook](https://learnopencv.com/xeus-cling-run-c-code-in-jupyter-notebook/), 不好用。这里面有讲怎么在notebook里面配置opencv.
2. [https://hub.docker.com/r/sehrig/cling](https://hub.docker.com/r/sehrig/cling), 这个能用，跑命令`run -it -p 8888:8888 sehrig/cling jupyter-notebook` 就能运行起来, 如果要加载本地目录，可以用v映射. 这个docker是用[这里的脚本](https://github.com/ComputationalRadiationPhysics/xeus-cling-cuda-container)生成的。
   1. 这里有[Latest docker file](https://hub.docker.com/layers/sehrig/cling/latest/images/sha256-cb6a7e78362355b6a665aeacfefff90257ffe12a3174fa678688d74663e12e4c?context=explore)
3. [Xeus Cling Installation from source](https://github.com/jupyter-xeus/xeus-cling/), github里面的安装教程安装以后还是抛同样的问题, `envs/xeus-cling/include/c++/v1/cstdlib:120:9: error: no member named 'strtoull' in the global namespace`.
   1.  如果编译的时候找不到CC, CXX编译器，可以通过下面的方法设置。https://stackoverflow.com/a/41381007
   		~~~sh
   		xcrun -find c++
		xcrun -find cc
		export CC="/path/to/your/c/compiler/executable"
		export CXX="/path/to/your/cpp/compiler/executable"
   		~~~
1.


一些有用的插件.
   1. [jupyter_contrib_nbextension](https://github.com/ipython-contrib/jupyter_contrib_nbextensions), 插件管理器, 有很多插件.
      1. 这个会在notebook加一个tab，里面列出一些知名的插件。要使用某插件需要在命令行打开。
      2. 可以通过
   2. [xtensor](https://github.com/xtensor-stack/xtensor), 一个可以处理numpy数据的c++库. The C++ support in Jupyter is powered by the xeus-cling C++ kernel. Together with xeus-cling, xtensor enables a similar workflow to that of NumPy with the IPython Jupyter kernel.
   3. [xwidgets](https://github.com/QuantStack/xwidgets/)A C++ backend for the Jupyter interactive widgets.
   4. JupyterLab, 高级版的Jupyter Notebook. 它有一个文件浏览器，可以预览各种文件， https://zhuanlan.zhihu.com/p/87403131。
   5.  https://github.com/sylabs/singularity, open source container platform.
   6.  https://github.com/NVIDIA/hpc-container-maker, 一个生成docker文件的工具.