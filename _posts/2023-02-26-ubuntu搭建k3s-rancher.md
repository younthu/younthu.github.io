---
layout: post
title: Ubuntu搭建K3S+rancher
date: 2023-02-26 09:45 +0800
excerpt: "在Ubuntu上搭建K3S+rancher"
---


# Mac上搭建
k3s不能直接跑在Mac上，在Mac上可以通过K3D搭建，把k3s跑在容器里。
1. 可以通过`.localhost`来访问`127.0.0.1`
2. `brew install k3d kubectl helm`
3. 


# 在linux上搭建

1. 搭建云服务, ubuntu系统.
2. 安装docker: `apt install docker.io`
3. 安装k3s master node: 
   ~~~sh
    curl -sfL https://get.k3s.io | sh -s - --docker # 自动安装， 并且以docker为容器运行时，默认为containerd.

    sudo k3s kubectl get nodes # confirm it is running
    cat /var/lib/rancher/k3s/server/node-token # 获取master node token
   ~~~
   K3s 包含并默认为containerd， 一个行业标准的容器运行时。要使用 Docker 而不是 containerd,可以用下面两种方法:
   1. 使用`--docker`选项安装K3s: `curl -sfL https://get.k3s.io | sh -s - --docker`
   2. 在 K3s 节点上安装 Docker。可以使用 Rancher 的一个Docker 安装脚本来安装 Docker：`curl https://releases.rancher.com/install-docker/19.03.sh | sh`
   3. 参考[使用 Docker 作为容器运行时](https://docs.rancher.cn/docs/k3s/advanced/_index/).
   卸载k3s: `/usr/local/bin/k3s-uninstall.sh`
4. 查看containers: ` k3s ctr task ls`
5. 安装K3S worker nodes:
   需要用到上一步在master node获取到的master node token.
   ~~~sh
   curl -sfL http://get.k3s.io | K3S_URL=https://<master_IP>:6443 K3S_TOKEN=<join_token> sh -s - --docker # 用docker作为运行时。默认是 containerd作为运行时.
   ~~~

6. install helm:
   ~~~sh
   curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 chmod 700 get_helm.sh ./get_helm.sh
   ~~~

   Notes: helm包下载很慢，容易断，如果脚本安装不了，就本地下载helm tar.gz, 然后scp拷贝到服务器上，然后解压安装，官方有文档:
   ~~~sh
   tar -zxvf helm-v3.0.0-linux-amd64.tar.gz
   mv linux-amd64/helm /usr/local/bin/helm
   ~~~
7.  更新helm repo
   ~~~sh
   helm repo add stable https://charts.helm.sh/stable
   helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
   helm repo update
   ~~~
8.  (optional)安装 [cert-manager](https://cert-manager.io/docs/installation/kubectl/#installing-with-regular-manifests), `kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.0/cert-manager.yaml`
    1.  `kubectl create namespace cattle-system && kubectl create namespace cert-manager`
9.  通过helm安装rancher: 

   ~~~sh
   # 注意rancher.localhost, localhost后缀是可以自动解析到本地127.0.0.1的
   helm install rancher rancher-stable/rancher \
   --namespace cattle-system \
   --set hostname=rancher.localhost \
   --set bootstrapPassword=verySecretivePasswordIndeed \
   --set ingress.tls.source=secret
   ~~~

   或者

   ~~~sh
   helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=k3s-rancher.localhost
   kubectl -n cattle-system rollout status deploy/rancher
   kubectl -n cattle-system get deploy rancher
   ~~~

10. 获取setup link: `echo https://rancher.mydomain.com/dashboard/?setup=$(kubectl get secret --namespace cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}')`
   1. helm版本rancher获取rancher登录密码: `k3s kubectl get secret --namespace cattle-system bootstrap-secret -o go-template='{{.data.bootstrapPassword|base64decode}}{{"\n"}}'`
   2. rancher.ilibrary.me账号密码: `admin/BtfEQI1E7yajt9lx`


# 在Rancher里创建cluster

参考[multiple k8s cluster management with rancher k3s lightweight k8s cluster for edge](https://selectfrom.dev/multiple-k8s-cluster-management-with-rancher-k3s-lightweight-k8s-cluster-for-edge-and-eea1f71175d0)
1. 按照说明里做。
2. 做了以后可能要等1个多小时cluster才会从reconciling变成active.
3. 变成active以后可以按页面的说明部署一个hello world玩玩。


# Notes
1. `Error: INSTALLATION FAILED: repo rancher-stable not found`
   1. helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
2. `Error: INSTALLATION FAILED: Kubernetes cluster unreachable: Get "http://localhost:8080/version": dial tcp [::1]:8080: connect: connection refused`
   1. export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
3. `Error: INSTALLATION FAILED: chart requires kubeVersion: < 1.25.0-0 which is incompatible with Kubernetes v1.25.6+k3s1`
   1. 安装指定版本: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.23.16+k3s1 sh -`
   2. 如果curl命令超时，可以手动下载k3s binary, 放`/usr/local/bin`目录下, 然后再运行上面的curl命令，脚本会跳过k3s下载步骤，走后面的配置流程。
4. rainbow部署以后通过ingress发布，https://ranxxx.ixxx.me/rainbow不工作。
   1. chart里面的host写的不对，改成目标host就好了。
5. `Error: INSTALLATION FAILED: unable to build kubernetes objects from release manifest: resource mapping not found for name: "rancher" namespace: "" from "": no matches for kind "Issuer" in version "cert-manager.io/v1" ensure CRDs are installed first`
   1. 
6. 



# 手动打包部署


1. 创建chart: `helm create mychart`
2. 打包: `helm package mychart` 或者指定输出的版本`helm package mychart --version=1.0.1`
3. 上传`mychart-1.0.1.tgz`到服务器: `scp mychart-1.0.1.tgz root@xxxx.com:~/`
4. ssh到服务器, 安装chart:
   ~~~sh
   helm install mychart ./mychart-1.0.1.tgz
   # or
   helm install -n my-app --namespace myspace mychart-1.0.1.tgz
   # or
   tar -zxvf mychart-1.0.1.tgz
   helm install -n my-app --namespace myspace mychart/
   ~~~
5. 如果有在线tgz包，也可以在命令行直接安装在线包: `helm install jenkins https://example.com/charts/jenkins-1.2.3.tgz`

# 自动打包部署
[参考Rancher官方CI/CD流水线文档](https://docs.rancher.cn/docs/rancher2/pipelines/_index#%E9%85%8D%E7%BD%AE%E6%B5%81%E6%B0%B4%E7%BA%BF)

1. 进入Rancher CD页面
2. 点击`git repos` -> `Create`, 
3. 填名称
4. 填git repo地址: `git@github.com:xxxxx/myk8s.git`, `xxxxx/myk8s`代表个人github repo.
   1. Rancher有个[官方example](https://docs.rancher.cn/docs/rancher2/pipelines/example-repos/_index): `https://github.com/rancher/pipeline-example-go.git`
5. 手动生成ssh key pair, 把private/pub key填入rancher
6. 把pub key设置到repo的deploy keys里面去。

# Rancher管理

1. 

# 删除Rancher
1. 列出所有安装好的App: `helm list -A`
2. 删除指定namespace下面的指定App: `helm uninstall rancher -n cattle-system`
3. 

# 删除k3d
1. k3d是一个binary, 它会管理所有k3s相关的容器. 
2. `which k3d`, 查看k3d的路径.
3. `k3d cluster delete -a`, 删除所有k3d创建的资源.
   
# Ref
1. https://computingforgeeks.com/install-kubernetes-on-ubuntu-using-k3s/
2. https://selectfrom.dev/multiple-k8s-cluster-management-with-rancher-k3s-lightweight-k8s-cluster-for-edge-and-eea1f71175d0 
3. [K8s - Kubernetes重要概念介绍（Cluster、Master、Node、Pod、Controller、Service、Namespace）](https://www.hangge.com/blog/cache/detail_2428.html)
4. [Kubernetes指南](https://feisky.gitbooks.io/kubernetes/content/introduction/)
5. [单机部署k3s](https://jasonkayzk.github.io/2022/10/21/%E5%8D%95%E6%9C%BA%E9%83%A8%E7%BD%B2k3s/), 提供了一个sh, [k3s-helm-install.sh](https://gist.github.com/JasonkayZK/670d578f9bb494c56c6061466dd7314f)
6. [Autok3s](https://github.com/cnrancher/autok3s), Rancher 中国团队推出了一款针对 K3s 的效率提升工具：AutoK3s。只需要输入一行命令，即可快速创建 K3s 集群并添加指定数量的 master 节点和 worker 节点. 
7. [How to install rancher on k3s](https://vmguru.com/2021/04/how-to-install-rancher-on-k3s/)
8. [【大强哥-k8s从入门到放弃12】Deployment资源详解](https://zhuanlan.zhihu.com/p/126292353), 对K8S基本概念讲得很清楚。
9.  [Kubernetes + Rancher Cluster Manager 2.6 on your macOS laptop with k3d/k3s in 5 min](https://itnext.io/kubernetes-rancher-cluster-manager-2-6-on-your-macos-laptop-with-k3d-k3s-in-5-min-8acdb94f3376)
10. [打包Helm Chart](https://blog.yowko.com/helm-package/)
11. [Rancher CI/CD 官方流水线](https://docs.rancher.cn/docs/rancher2/pipelines/_index)
