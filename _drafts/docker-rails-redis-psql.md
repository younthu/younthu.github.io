---
layout: post
title: docker rails redis psql
categories: 技术
tags:
- Rails
- Docker
---
# 环境需求
# 基本步骤
# 注意事项
# 概念
## docker-compose 语法
###links
~~~
链接到其它服务中的容器。使用服务名称（同时作为别名）或服务名称：服务别名 （SERVICE:ALIAS） 格式都可以。
links:
- db
- db:database
- redis
使用的别名将会自动在服务容器中的 /etc/hosts 里创建。例如：
172.17.2.186 db
相应的环境变量也将被创建。
~~~

### ports
暴露端口信息。
使用宿主：容器 （HOST:CONTAINER）格式或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。

### expose
暴露端口，但不映射到宿主机，只被连接的服务访问。
仅可以指定内部端口为参数
expose:
- "3000"
- "8000"

### volumes
卷挂载路径设置。可以设置宿主机路径 （HOST:CONTAINER） 或加上访问模式(HOST:CONTAINER:ro)，后者对于容器来说，数据卷是只读的，这样可以有效保护宿主机的文件系统。
~~~
volumes:
- /var/lib/mysql
- cache/:/tmp/cache
- ~/configs:/etc/configs/:ro
~~~

### env_file
从文件中获取环境变量，可以为单独的文件路径或列表。
如果通过 docker-compose -f FILE 指定了模板文件，则 env_file 中路径会基于模板文件路径。
如果有变量名称与 environment 指令冲突，则以后者为准。
env_file: .env
env_file:
- ./common.env
- ./apps/web.env
- /opt/secrets.env
环境变量文件中每一行必须符合格式，支持 # 开头的注释行。
  # common.env: Set Rails/Rack environment
RACK_ENV=development

### depends_on

在使用 Compose 时，最大的好处就是少打启动命令，但是一般项目容器启动的顺序是有要求的，如果直接从上到下启动容器，必然会因为容器依赖问题而启动失败。
例如在没启动数据库容器的时候启动了应用容器，这时候应用容器会因为找不到数据库而退出，为了避免这种情况我们需要加入一个标签，就是 depends_on，这个标签解决了容器的依赖、启动先后的问题。
例如下面容器会先启动 redis 和 db 两个服务，最后才启动 web 服务：

~~~
version: '2'
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
~~~
注意的是，默认情况下使用 docker-compose up web 这样的方式启动 web 服务时，也会启动 redis 和 db 两个服务，因为在配置文件中定义了依赖关系。

# 参考
1. [docker-compose.yml 语法说明](http://www.cnblogs.com/freefei/p/5311294.html)
1. [Docker Compose 配置文件详解](http://www.jianshu.com/p/2217cfed29d7)
