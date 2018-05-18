---
layout: post
title: chromium compilation
---
Firefox代码量小，编译起来也快，只要十多分钟就编译完成了,也不占用磁盘空间, 编译完产生的文件大概6G左右。chromium代码量大，编译也巨慢无比，按天计算的，临时文件具多，磁盘占用在73G(算上源码), 我Mac第一次编译的时候磁盘就爆了。

[Mac下编译chromium](https://chromium.googlesource.com/chromium/src/+/master/docs/mac_build_instructions.md)

1.
1. `source ~/pyVirtualEnv/nltk2/bin/activate`
1. `fetch chromium`
1. `gclient sync`
1. `gn gen out/Default`
1. `ninja -C out/Default chrome`
1. `out/Default/Chromium.app/Contents/MacOS/Chromium`
1. `gn gen out/gn --ide=xcode`
1. `open out/gn/ninja/all.xcworkspace`

源码checkout erro 1

~~~
fetch chromium
Running: gclient root
/Users/andrewy/sourcecode/github/.gclient_entries missing, .gclient file in parent directory /Users/andrewy/sourcecode/github might not be the file you want to use.
Your current directory appears to already contain, or be part of,
a checkout. "fetch" is used only to get new checkouts. Use
"gclient sync" to update existing checkouts.

Fetch also does not yet deal with partial checkouts, so if fetch
failed, delete the checkout and start over (crbug.com/230691).
~~~

解决办法

删除`.gclient`,然后重新`fetch chromium`


gclient sync错误

~~~
gclient sync
/Users/andrewy/sourcecode/github/chromium/.gclient_entries missing, .gclient file in parent directory /Users/andrewy/sourcecode/github/chromium might not be the file you want to use.
Syncing projects: 100% (74/74), done.

________ running 'vpython src/build/landmines.py' in '/Users/andrewy/sourcecode/github/chromium'
[E2017-12-14T17:12:12.255516+08:00 18072 0 annotate.go:238] goroutine 1:
[E2017-12-14T17:12:12.255557+08:00 18072 0 annotate.go:238] #0 go.chromium.org/luci/vpython/python/find.go:88 - python.Find()
[E2017-12-14T17:12:12.255570+08:00 18072 0 annotate.go:238]   annotation #0:
[E2017-12-14T17:12:12.255578+08:00 18072 0 annotate.go:238]     reason: no Python found
[E2017-12-14T17:12:12.255585+08:00 18072 0 annotate.go:238]   annotation #1:
[E2017-12-14T17:12:12.255592+08:00 18072 0 annotate.go:238]     reason: failed to get version for: "/Users/andrewy/anaconda/envs/ml2.7/bin/python2.7"
[E2017-12-14T17:12:12.255604+08:00 18072 0 annotate.go:238]   annotation #2:
[E2017-12-14T17:12:12.255611+08:00 18072 0 annotate.go:238]     reason: failed to parse version from: "2.7.13 :: Anaconda 4.4.0 (x86_64)"
[E2017-12-14T17:12:12.255618+08:00 18072 0 annotate.go:238]   annotation #3:
[E2017-12-14T17:12:12.255625+08:00 18072 0 annotate.go:238]     reason: non-canonical Python version string: "2.7.13 :: Anaconda 4.4.0 (x86_64)"
[E2017-12-14T17:12:12.255636+08:00 18072 0 annotate.go:238]
[E2017-12-14T17:12:12.255643+08:00 18072 0 annotate.go:238] #1 go.chromium.org/luci/vpython/venv/config.go:253 - venv.(*Config).resolvePythonInterpreter()
[E2017-12-14T17:12:12.255650+08:00 18072 0 annotate.go:238]   reason: could not find Python for: 2.7.0
[E2017-12-14T17:12:12.255661+08:00 18072 0 annotate.go:238]
[E2017-12-14T17:12:12.255668+08:00 18072 0 annotate.go:238] #2 go.chromium.org/luci/vpython/venv/config.go:175 - venv.(*Config).makeEnv()
[E2017-12-14T17:12:12.255675+08:00 18072 0 annotate.go:238]   reason: failed to resolve system Python interpreter
[E2017-12-14T17:12:12.255682+08:00 18072 0 annotate.go:238]
[E2017-12-14T17:12:12.255702+08:00 18072 0 annotate.go:238] #3 go.chromium.org/luci/vpython/venv/venv.go:122 - venv.With()
[E2017-12-14T17:12:12.255710+08:00 18072 0 annotate.go:238]   reason: failed to initialize empty probe environment
[E2017-12-14T17:12:12.255717+08:00 18072 0 annotate.go:238]
[E2017-12-14T17:12:12.255724+08:00 18072 0 annotate.go:238] #4 go.chromium.org/luci/vpython/run.go:72 - vpython.Run()
[E2017-12-14T17:12:12.255732+08:00 18072 0 annotate.go:238] #5 go.chromium.org/luci/vpython/application/application.go:261 - application.(*application).mainImpl()
[E2017-12-14T17:12:12.255748+08:00 18072 0 annotate.go:238] #6 go.chromium.org/luci/vpython/application/application.go:349 - application.(*Config).Main.func1()
[E2017-12-14T17:12:12.255756+08:00 18072 0 annotate.go:238] #7 go.chromium.org/luci/vpython/application/support.go:45 - application.run()
[E2017-12-14T17:12:12.255763+08:00 18072 0 annotate.go:238] #8 go.chromium.org/luci/vpython/application/application.go:348 - application.(*Config).Main()
[E2017-12-14T17:12:12.255771+08:00 18072 0 annotate.go:238] #9 vpython/main.go:92 - main.mainImpl()
[E2017-12-14T17:12:12.255778+08:00 18072 0 annotate.go:238] #10 vpython/main.go:98 - main.main()
[E2017-12-14T17:12:12.255785+08:00 18072 0 annotate.go:238] #11 runtime/proc.go:185 - runtime.main()
[E2017-12-14T17:12:12.255796+08:00 18072 0 annotate.go:238] #12 runtime/asm_amd64.s:2337 - runtime.goexit()
Error: Command 'vpython src/build/landmines.py' returned non-zero exit status 1 in /Users/andrewy/sourcecode/github/chromium
~~~

gclient 依赖于python2.7, Anaconda环境有问题, 新建的python2也抛同样的错误。用virtualenv 切换到python2就可以工作了。virtualenv切换python2 `source ~/pyVirtualEnv/nltk2/bin/activate`

# 参考

1. [Mac OS X Debugging Magic](https://developer.apple.com/library/content/technotes/tn2124/_index.html)
1. [Debugging Chromium on macOS](https://dev.chromium.org/developers/how-tos/debugging-on-os-x)
