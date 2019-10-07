---
layout: post
title: godaddy ssl证书续费, nginx配置
excerpt: '最主要的坑是证书部署以后因为key文件编码的问题会导致证书加载失败。抛错误"SSL error:0906D06C:PEM routines:PEM_read_bio:no start line"'
---

1. 去Godaddy续费
2. 生成新证书
    1. 这一步要生成key pair，可以选择自己生成，也可以选择让godaddy自己生成。我选择godaddy生成，然后下载保存供后面用.
    2. 然后一路按照提示就可以生成新证书了。
3. 下载证书，这是一个zip包, 里面两个crt一个pem. 有一个crt和pem内容是一样的，属于你网站专属的cert. 另外一个crt是父证书的打包，在后面会要合并到你网站专属的cert后面.
4. 合并crt文件
    1. 合并原因是某些浏览器需要完整的pub key chain.
    2. godday下载下来的zip文件里面有一个类似`e0e44ecf6b5dfab.crt`和一个类似`gd_bundle-g2-g1.crt`, 通过命令`cat e0e44ecf6b5dfab.crt gd_bundle-g2-g1.crt > domain.com.crt`得到一个新的crt文件
5. 上传private key和合并后的crt文件到服务器
6. 修改nginx里面 ssl_certificate 和 ssl_certificate_key, 分别指向合并后的crt和private key.
   ~~~
    server {

    listen   443;

    ssl    on;
    ssl_certificate    /etc/ssl/your_domain_name.pem; (or bundle.crt)
    ssl_certificate_key    /etc/ssl/your_domain_name.key;

    server_name your.domain.com;
    access_log /var/log/nginx/nginx.vhost.access.log;
    error_log /var/log/nginx/nginx.vhost.error.log;
    location / {
        root   /home/www/public_html/your.domain.com/public/;
        index  index.html;
    }

    }
   ~~~
1. 重启nginx: `service nginx restart`

## cloudflare 免费证书
1. 在SSL/TLS -> Origin Server下面 'Create Certificate', 生成一个Origin Certificates, 也就是self-signed certificate
2. 把pub key 和 private key分别保存为 domain.pem, domain.key
3. 在nginx下面配置ssl
4. 这个是自签名的证书，浏览器是不认的。需要走cloudflare的代理才能正常工作。方法就是在cloudflare -> DNS 下面 A记录或者C记录最后的'Proxy status'栏的'DNS only'点击一下，变成 'proxied'. 具体见[stackoverflow](https://community.cloudflare.com/t/https-certificate-not-trusted/3610/12)
## 问题跟踪

1. 重启nginx出错,抛下面错误
    ~~~
    SSL_CTX_use_PrivateKey_file("/var/www/dg/shared/config/dealglobe.com.key") failed (SSL: error:0906D06C:PEM routines:PEM_read_bio:no start line:Expecting: ANY PRIVATE KEY error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib)
    ~~~

    原因是nginx不能正确读取utf8文件，可能是读了UTF-8 BOM文件. [问题参考 stack overflow](参考： https://stackoverflow.com/questions/43729770/nginx-godaddy-ssl?rq=1).
    
    如果想了解BOM的坑可以看[UTF-8 BOM的坑](/2019/06/28/utf-8-bom的坑)

    __解决方法:__ 

    把key文件编码由utf8转换成ANSI就可以了， 

    编码变换:
    ~~~
    UTF-8 BOM的文件无法用iconv做转换。
    可以用vim新建一个文件，把key内容贴进去就可以得到ASCII文件.
    ~~~


# 参考
1. [nginx configure ssl](https://www.digicert.com/csr-ssl-installation/nginx-openssl.htm#ssl_certificate_install)
