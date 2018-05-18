---
layout: post
title: 为终端设置Shadowsocks代理
---

# 基本配置
请参考技术小黑屋的文章

# 基本环境安装
Install & configure shadowsocks client
1. sudo apt install shadowsocks
2. nohup sslocal -s <52.193.204.xxx> -p 8105 -l 1080 -k <your password> -t 600 -m aes-256-cfb &

install & configure polipo
1. sudo apt-get install polipo
2. sudo vim /etc/polipo/config # add below content to polio config file socksParentProxy = "localhost:1080"
       socksProxyType = socks5
3. sudo service polipo restart

testing it:
~~~
https_proxy=http://localhost:8123 http_proxy=http://localhost:8123 curl google.com
~~~

# 给其它设备提供代理服务
默认polipo只给本机提供代理。如果要给局域网内(或者公网，如果你是在公网服务器上)机器提供代理服务器得绑定一个泛ip地址:

Mac配置(/usr/local/opt/polipo/homebrew.mxcl.polipo.plist)
~~~
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.polipo</string>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/opt/polipo/bin/polipo</string>
        <string>socksParentProxy=localhost:1080</string>
        <string>proxyAddress="0.0.0.0"</string>
        <string>proxyPort=8123</string>
    </array>
    <!-- Set `ulimit -n 20480`. The default OS X limit is 256, that's
         not enough for Polipo (displays 'too many files open' errors).
         It seems like you have no reason to lower this limit
         (and unlikely will want to raise it). -->
    <key>SoftResourceLimits</key>
    <dict>
      <key>NumberOfFiles</key>
      <integer>20480</integer>
    </dict>
  </dict>
</plist>
~~~
# 问题记录
1. `zsh: command not found: hp`
2.
~~~
alias hp="http_proxy=http://localhost:8123 https_proxy=http://localhost:8123"
alias hps="https_proxy=http://localhost:8123"
~~~
# Refs
[技术小黑屋](http://droidyue.com/blog/2016/04/04/set-shadowsocks-proxy-for-terminal/)
