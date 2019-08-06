---
layout: post
title: 关于nginx下assets配置,以及InvalidAuthenticityToken的问题
categories:
- 技术
tags:['nginx','rails']
excerpt: ''
---

# 常见两个问题
平时喜欢写代码，搞一些好玩的东西，有api服务，也有web站点。为了节省服务器成本，很多站点都部署到一个服务器上。所有的站点共用一个nginx, 然后经常遇到两个问题:

1. assets目录下的东西找不到, 404
2. 用户登录的时候抛 `InvalidAuthenticityToken`异常，即使用户名密码是对的也这样。

第一个问题是因为root和资源文件寻找规则未配置好。

## 解决assets找不到的问题
nginx配置如下:

~~~
server {
  listen 80;
  server_name domain.com;
  root /var/www/web/current/public;

  # serve static (compiled) assets directly if they exist (for rails production)
  location ~ ^/(assets|images|javascripts|stylesheets|swfs|system)/ {
    try_files $uri @app;

    access_log off;
    gzip_static on; # to serve pre-gzipped version

    expires max;
    add_header Cache-Control public;

    # Some browsers still send conditional-GET requests if there's a
    # Last-Modified header or an ETag header even if they haven't
    # reached the expiry date sent in the Expires header.
    add_header Last-Modified "";
    add_header ETag "";
    break;
  }

  location / {
    try_files $uri @app;
  }
  location @app  {
        proxy_pass http://127.0.0.1:6666;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
    }

}
~~~


重点在于 `location ~^/(assets|images|javascripts|stylesheets)/`， 这一行是匹配潜在的资源目录名，匹配上以后 `try_files $uri @app`会尝试直接根据uri查找静态文件，找到就直接返回，找不到就去@app这个规则里面找.

location匹配语法见本文后面章节.

## 转发 解决CSRF, InvalidAuthenticityToken的问题
新建的应用，配置好nginx proxy请求转发，我们往往会遇到host被替换成内部ip地址以后CSRF过不了, 抛`InvalidAuthenticityToken`异常。这种情况下只需要在设置proxy_pass的时候把 host带过去就可以了。

~~~
location @app  {
        proxy_pass http://127.0.0.1:6666;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
    }


~~~

# 参考: nginx配置基础知识
下面是一些基础的nginx配置知识.

## 最简单的nginx服务器

~~~
server {     // 虚拟主机server可以有多个      
  listen      *:80 default_server;
  server_name netguru.co;
  return 200 "Hello from netguru.co";
}
~~~

## nginx静态文件服务器

~~~
server {
    listen 80 default_server;
    listen [::]:443 ssl default_server;

    root /var/www/your_project_root
    server_name your_domain.com

    location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
~~~

## location指令
location [modifier] path

~~~
// modifier 如下，匹配优先级依次降低
=            - 精准match
^~          - 优先 match
~            - 正则 match(大小写敏感)
~*           - 正则 match(大小写不敏感)
no modifier - 前缀 match  
~~~

## try_files 指令
尝试不同的路径，找到一个路径就返回。

~~~
try_files $uri index.html =404;
~~~

所以对于 /foo.html 请求，它将尝试按以下顺序返回文件：
$uri ( /foo.html )
index.html
如果什么都没找到则返回 40