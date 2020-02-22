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