---
layout: post
title: 'matplotlib保存动画为mp4失败,convert: delegate failed ffmpeg error'
excerpt: '通过matplotlib.animation保存mp4动画的时候抛convert delegate failed ffmpeg error. 解决方法是重装ffmpeg, 然后link ffmpeg'
category: python
tags:
- python
- matplotlib
- Animation
---

## 问题
升级anaconda以后, Mac上通过`matplotlib.animation` 保存动画的时候抛错, 提示delegate failed。

部分动画相关的代码

~~~python
ani = animation.FuncAnimation(fig, draw, range(60), interval=300)

# ani.save('history.gif', fps=8, writer='imagemagick')
ani.save('history.mp4', fps=8, writer='imagemagick')
~~~

抛错:

~~~python
MovieWriter stderr:
convert: delegate failed `'ffmpeg' -nostdin -v -1 -i '%M%%d.jpg' '%u.%m' 2> '%u'' @ error/delegate.c/InvokeDelegate/1897.

---------------------------------------------------------------------------
CalledProcessError                        Traceback (most recent call last)
<ipython-input-4-fd5c8e78a3fb> in <module>
     44 
     45 # ani.save('history.gif', fps=8, writer='imagemagick')
---> 46 ani.save('history.mp4', fps=8, writer='imagemagick')
     47 # FFwriter = animation.FFMpegWriter(fps=8, codec="libx264")
     48 # ani.save('basic_animation1.mp4', writer = FFwriter )
~~~

## 解决办法

在命令行测试`ffmpeg` 命令，发现找不到`ffmpeg`命令。

用`brew install ffmpeg` 重新安装，还是找不到。 

再用`brew link ffmpeg` 链接一下，问题解决。

如果link ffmpeg有冲突，可以考虑用`brew link --overwrite ffmpeg` 强制链接。

## 解决办法2

用docker来启动`jupyter notebook`

~~~sh
docker run -i -t -p 8888:8888 -v $PWD:/opt/notebooks continuumio/anaconda3 /bin/bash -c "/opt/conda/bin/conda install jupyter -y --quiet && /opt/conda/bin/jupyter notebook --allow-root --notebook-dir=/opt/notebooks --ip='0.0.0.0' --port=8888 --no-browser"
~~~

上面的脚本会把当前目录加载到container然后做jupyter notebook的工作目录. 通过浏览器打开 http://localhost:8888 就可以打开notebook了。

需要注意的地方: 
1. 记得把host里面的 jupyter notebook关闭，要不然token验证会出错。
2. 用docker以后Mac上用到的中文字体会失效，记得安装字体，要不然中文字体都是变成框框。
3. 绘画功能还要安装imagemagick: `apt install imagemagick`
4. 当前这个镜像基于debian的, apt安装软件的时候回会非常慢，可以更新一下国内的源。下面是清华的源，注意该源把源码镜像注释掉了。 
    
    源地址: https://mirror.tuna.tsinghua.edu.cn/help/debian/, 可以选版本。

   ~~~
   # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free
   ~~~
   可以用`cat > /etc/apt/sources.list <<EOF` 命令把source更新到`/etc/apt/sources.list`里面去。记得备份sources.list。
5. 还要安装ffmpeg: `apt install ffmpeg` 。


## 内存不足的问题
在debian下可能会遇到下面的问题:

~~~
MovieWriter stderr:
convert-im6.q16: no images defined `history.mp4' @ error/convert.c/ConvertImageCommand/3258.

---------------------------------------------------------------------------
BrokenPipeError                           Traceback (most recent call last)
/opt/conda/lib/python3.7/site-packages/matplotlib/animation.py in saving(self, fig, outfile, dpi, *args, **kwargs)
    229         try:
--> 230             yield self
    231         finally:

/opt/conda/lib/python3.7/site-packages/matplotlib/animation.py in save(self, filename, writer, fps, dpi, codec, bitrate, extra_args, metadata, extra_anim, savefig_kwargs, progress_callback)
~~~

这是imagemagick[内存限制](https://github.com/ImageMagick/ImageMagick/issues/396)的问题，可以注释掉配置文件/etc/ImageMagick-6/policy.xml下面的几行：

~~~
# /etc/ImageMagick-6/policy.xml, 注释掉下面这几行

  <policy domain="resource" name="memory" value="256MiB"/>
  <policy domain="resource" name="map" value="512MiB"/>
  <policy domain="resource" name="width" value="16KP"/>
  <policy domain="resource" name="height" value="16KP"/>
~~~


