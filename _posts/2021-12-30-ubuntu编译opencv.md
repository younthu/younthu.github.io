---
layout: post
title: ubuntu编译opencv
date: 2021-12-30 22:26 +0800
---
ubuntu下面编译opencv, 有坑，记录一下.

根据官方教程编译，坑少一点。
1. 教程地址: https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html
2. 下载opencv: wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
3. 下载扩展包, opencv contrib: wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip
4. 解压并配置目录:
   ~~~sh
   	unzip opencv.zip
	unzip opencv_contrib.zip
	# Create build directory and switch into it
	mkdir -p build && cd build
   ~~~
5. 配置: `cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib-4.5.5/modules -D BUILD_opencv_python=OFF -D OPENCV_ENABLE_NONFREE=ON ../opencv-4.5.5`
   1. DOPENCV_EXTRA_MODULES_PATH, 编译扩展包。没有扩展包很多功能用不了。
   2. BUILD_opencv_python=OFF, 关闭python extension. 在ubuntu下python extension总是编译不过。先跳过。
   3. OPENCV_ENABLE_NONFREE=ON, 下载编译专利算法包。要不然surf算法用不了。
6. 编译: `make -j8`, 8线程编译.
3. 安装: `sudo make install`
