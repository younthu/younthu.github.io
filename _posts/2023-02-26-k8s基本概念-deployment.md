---
layout: post
title: K8S基本概念 - Deployment
date: 2023-02-26 10:30 +0800
---
# K8S基本概念
## 一、何为Deployment
Deployment是一个定义及管理多副本应用（即多个副本 Pod）的新一代对象，与Replication Controller相比，它提供了更加完善的功能，使用起来更加简单方便。

如果Pod出现故障，对应的服务也会挂掉，所以Kubernetes提供了一个Deployment的概念 ，目的是让Kubernetes去管理一组Pod的副本，也就是副本集 ，这样就能够保证一定数量的副本一直可用，不会因为某一个Pod挂掉导致整个服务挂掉。

Deployment 还负责在 Pod 定义发生变化时，对每个副本进行滚动更新（Rolling Update）。

这样使用一种 API 对象（Deployment）管理另一种 API 对象（Pod）的方法，在 k8s 中，叫作"控制器"模式（controller pattern）。Deployment 扮演的正是 Pod 的控制器的角色。

## 二、K8S创建Deployment资源流程
1. ==用户通过 kubectl 创建 Deployment。==
2. ==Deployment 创建 ReplicaSet。==
3. ==ReplicaSet 创建 Pod。==
   
对象的命名方式是:
~~~
子对象的名字 = 父对象名字 + 随机字符串或数字
~~~
## 三、创建Deployment
例1：
~~~yaml
apiVersion: apps/v1  #注意，如果用这个api就必须用标签选择器
kind: Deployment
metadata:
 name: nginx-deployment
spec:
 selector:
  matchLabels:
   app: nginx
 replicas: 2
 template:
  metadata:
   labels:
    app: nginx
  spec:
   containers:
   - name: nginx
     image: nginx:1.7.9
     ports:
     - containerPort: 80
~~~

例2：在上面yaml的基础上添加了volume
~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: nginx-deployment
spec:
 selector:
  matchLabels:
    app: nginx
 replicas: 2
 template:
  metadata:
   labels:
    app: nginx
  spec:
   containers:
   - name: nginx
     image: nginx:1.8
     ports:
     - containerPort: 80
     volumeMounts:
     - mountPath: "/usr/share/nginx/html"
       name: nginx-vol
   volumes:
   - name: nginx-vol
     emptyDir: {}
~~~

## 四、Deployment资源属性
metadata
> 指定一些meta信息，包括名字或标签之类的。
每一个 API 对象都有一个叫作 Metadata 的字段，这个字段是 API 对象的"标识"，即元数据，也是我们从 Kubernetes 里找到这个对象的主要依据。

labels
> Labels是最主要的字段,是一组 key-value 格式的标签,k8s中的所有资源都支持携带label,默认情况下，pod的label会复制rc的label
> k8s使用用户自定义的key-value键值对来区分和标识资源集合（就像rc、pod等资源），这种键值对称为label。
像 Deployment 这样的控制器对象，就可以通过这个 Labels 字段从 Kubernetes 中过滤出它所关心的被控制对象。

Annotations
> 在 Metadata 中，还有一个与 Labels 格式、层级完全相同的字段叫 Annotations，它专门用来携带 key-value 格式的内部信息。所谓内部信息，指的是对这些信息感兴趣的，是 Kubernetes 组件本身，而不是用户。所以大多数 Annotations，都是在 Kubernetes 运行过程中，被自动加在这个 API 对象上。

selector
> 过滤规则的定义，是在 Deployment 的"spec.selector.matchLabels"字段。一般称之为：Label Selector。
> pod的label会被用来创建一个selector，用来匹配过滤携带这些label的pods。
可以通过kubectl get请求这样一个字段来查看template的格式化输出：
~~~sh
# kubectl get rc my-nginx -o template --template="{{.spec.selector}}"
  map[app:nginx11]
~~~

spec
> 一个 k8s 的 API 对象的定义，大多可以分为 Metadata 和 Spec 两个部分。前者存放的是这个对象的元数据，对所有 API 对象来说，这一部分的字段和格式基本上是一样的；而后者存放的，则是属于这个对象独有的定义，用来描述它所要表达的功能。
> 这里定义需要两个副本，此处可以设置很多属性，主要是受此Deployment影响的Pod的选择器
> spec 选项的template其实就是对Pod对象的定义
> 可以在Kubernetes v1beta1 API 参考中找到完整的Deployment可指定的参数列表.

replicas
> 定义的 Pod 副本个数 (spec.replicas) 是：2

template
> 定义了一个 Pod 模版（spec.template），这个模版描述了想要创建的 Pod 的细节。例子里，这个 Pod 里只有一个容器，这个容器的镜像（spec.containers.image）是 nginx:1.7.9，这个容器监听端口（containerPort）是 80。

volumes
> 是属于 Pod 对象的一部分。需要修改 template.spec 字段
> 例3中，在 Deployment 的 Pod 模板部分添加了一个 volumes 字段，定义了这个 Pod 声明的所有 Volume。它的名字叫作 nginx-vol，类型是 emptyDir。
> 关于emptyDir 类型：
> ​  等同于 Docker 的隐式 Volume 参数，即：不显式声明宿主机目录的 Volume。所以，Kubernetes 也会在宿主机上创建一个临时目录，这个目录将来就会被绑定挂载到容器所声明的 Volume 目录上。
>​ k8s 的 emptyDir 类型，只是把 k8s 创建的临时目录作为 Volume 的宿主机目录，交给了 Docker。这么做的原因，是 k8s 不想依赖 Docker 自己创建的那个 _data 目录。

volumeMounts
> Pod 中的容器，使用的是 volumeMounts 字段来声明自己要挂载哪个 Volume，并通过 mountPath 字段来定义容器内的 Volume 目录，比如：/usr/share/nginx/html。

hostPath
> k8s 也提供了显式的 Volume 定义，它叫做 hostPath。比如下面的这个 YAML 文件：
> ...  
>     volumes:
>      - name: nginx-vol
>        hostPath: 
>         path: /var/data
这样，容器 Volume 挂载的宿主机目录，就变成了 /var/data

Configmap
> configmap是k8s的一个配置管理组件，可以将配置以key-value的形式传递，通常用来保存不需要加密的配置信息，加密信息则需用到Secret，主要用来应对以下场景：
> 使用k8s部署应用，当你将应用配置写进代码中，就会存在一个问题，更新配置时也需要打包镜像，configmap可以将配置信息和docker镜像解耦。
> 使用微服务架构的话，存在多个服务共用配置的情况，如果每个服务中单独一份配置的话，那么更新配置就很麻烦，使用configmap可以友好的进行配置共享。
> 其次，configmap可以用来保存单个属性，也可以用来保存配置文件。

RKE
> RKE 全称是 Rancher Kubernetes Engine。可以通过 CLI 的方式独立于 Rancher 2.x 使用。可以在安装好 docker 的 linux 主机上，快速方便的搭建 Kubernetes 集群。在搭建生产可用的 Kubernetes 集群的工具里，RKE 的易用性应该是最好的。关于 RKE 和 Rancher 的关系，RKE 是 Rancher 2.x 中的一个重要组成部分，在 UI 上通过“自定义主机”创建的集群和通过“主机驱动”创建的集群，都是 Rancher Server 调用 RKE 模块来实现的。我们一般叫这种集群为 RKE 集群。英文文档和 Release Notes 里叫 Rancher-Launched Kubernetes cluster。

应用服务网格（Istio）
> Istio 是一个提供连接、保护、控制以及观测功能的开放平台。
> Rancher 集成了应用服务网格，支持完整的生命周期管理和流量治理能力，兼容 Kubernetes 和 Istio 生态。开启应用服务网格后即可提供非侵入的智能流量治理解决方案，其功能包括负载均衡、熔断、限流等多种治理能力。应用服务网格内置金丝雀、蓝绿等多种灰度发布流程，提供一站式自动化的发布管理。

# Refs
1. [【大强哥-k8s从入门到放弃12】Deployment资源详解](https://zhuanlan.zhihu.com/p/126292353), 对K8S基本概念讲得很清楚。