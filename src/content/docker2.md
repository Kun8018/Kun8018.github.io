---
###title: Containerd学习
date: 2020-05-02 21:40:33
categories: IT
tags:
    - 
toc: true
thumbnail: http://cdn.kunkunzhang.top/docker.png
---

　　Containerd容器

<!--more-->

## Containerd

作为接替 Docker 运行时的 Containerd 在早在 Kubernetes1.7 时就能直接与 Kubelet 集成使用，只是大部分时候我们因熟悉 Docker，在部署集群时采用了默认的 dockershim。在 `V1.24` 起的版本的 kubelet 就彻底移除了 `dockershim`，改为默认使用 `Containerd` 了，当然也可以使用 `cri-dockerd` 适配器来将 `Docker Engine` 与 Kubernetes 集成

更换 Containerd 后，以往我们常用的 docker 命令也不再使用，取而代之的分别是 `crictl` 和 `ctr` 两个命令客户端

`crictl` 是遵循 CRI 接口规范的一个命令行工具，通常用它来检查和管理 `kubelet` 节点上的容器运行时和镜像。

`ctr` 是 `containerd` 的一个客户端工具

`ctr -v` 输出的是 `containerd` 的版本，`crictl -v` 输出的是当前 k8s 的版本，从结果显而易见你可以认为 `crictl` 是用于 `k8s` 的

一般来说你某个主机安装了 k8s 后，命令行才会有 crictl 命令。而 ctr 是跟 k8s 无关的，你主机安装了 containerd 服务后就可以操作 ctr 命令

使用 `crictl` 命令之前，需要先配置 `/etc/crictl.yaml` 如下

```yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
image-endpoint: unix:///run/containerd/containerd.sock
timeout: 10
debug: false
```

也可以通过命令进行设置

```shell
crictl config runtime-endpoint unix:///run/containerd/containerd.sock
crictl config image-endpoint unix:///run/containerd/containerd.sock
```

命令表格

| 命令                     | docker             | ctr（containerd）            | crictl（kubernetes） |
| ------------------------ | ------------------ | ---------------------------- | -------------------- |
| 查看运行的容器           | docker ps          | ctr task ls/ctr container ls | crictl ps            |
| 查看镜像                 | docker images      | ctr image ls                 | crictl images        |
| 查看容器日志             | docker logs        | 无                           | crictl logs          |
| 查看容器数据信息         | docker inspect     | ctr container info           | crictl inspect       |
| 查看容器资源             | docker stats       | 无                           | crictl stats         |
| 启动 / 关闭已有的容器    | docker start/stop  | ctr task start/kill          | crictl start/stop    |
| 运行一个新的容器         | docker run         | ctr run                      | 无（最小单元为 pod） |
| 打标签                   | docker tag         | ctr image tag                | 无                   |
| 创建一个新的容器         | docker create      | ctr container create         | crictl create        |
| 导入镜像                 | docker load        | ctr image import             | 无                   |
| 导出镜像                 | docker save        | ctr image export             | 无                   |
| 删除容器                 | docker rm          | ctr container rm             | crictl rm            |
| 删除镜像                 | docker rmi         | ctr image rm                 | crictl rmi           |
| 拉取镜像                 | docker pull        | ctr image pull               | ctictl pull          |
| 推送镜像                 | docker push        | ctr image push               | 无                   |
| 登录或在容器内部执行命令 | docker exec        | 无                           | crictl exec          |
| 清空不用的容器           | docker image prune | 无                           | crictl rmi --prune   |

由于 Containerd 也有 namespaces 的概念，对于上层编排系统的支持，`ctr` 客户端 主要区分了 3 个命名空间分别是 `k8s.io`、`moby` 和 `default`，以上我们用 `crictl` 操作的均在 `k8s.io` 命名空间，使用 `ctr` 看镜像列表就需要加上 - n 参数。crictl 是只有一个 `k8s.io` 命名空间，但是没有 - n 参数

## nerdctl

nerdctl 是一个与 docker cli 风格兼容的 containerd 客户端工具，而且直接兼容 docker compose 的语法的，这就大大提高了直接将 containerd 作为本地开发、测试或者单机容器部署使用的效率。推荐使用 nerdctl，使用效果与 docker 命令的语法一致

https://github.com/containerd/nerdctl

安装

```shell
wget https://github.com/containerd/nerdctl/releases/download/v0.22.2/nerdctl-0.22.2-linux-amd64.tar.gz
# 解压
tar -xf nerdctl-0.22.2-linux-amd64.tar.gz

ln -s /opt/k8s/nerdctl/nerdctl /usr/local/bin/nerdctl
```

和 `docker run` 类似可以使用 `nerdctl run` 命令运行容器，例如

```shell
➜  ~ nerdctl run -d -p 80:80 --name=nginx --restart=always nginx:alpine
docker.io/library/nginx:alpine:                                                   resolved       |++++++++++++++++++++++++++++++++++++++|
index-sha256:bead42240255ae1485653a956ef41c9e458eb077fcb6dc664cbc3aa9701a05ce:    done           |++++++++++++++++++++++++++++++++++++++| manifest-sha256:ce6ca11a3fa7e0e6b44813901e3289212fc2f327ee8b1366176666e8fb470f24: done           |++++++++++++++++++++++++++++++++++++++| config-sha256:7ce0143dee376bfd2937b499a46fb110bda3c629c195b84b1cf6e19be1a9e23b:   done           |++++++++++++++++++++++++++++++++++++++| elapsed: 5.3 s                                                                    total:  3.1 Ki (606.0 B/s)                                       6e489777d2f73dda8a310cdf8da9df38353c1aa2021d3c2270b30eff1806bcf8
```



## skopeo

**[skopeo](https://github.com/containers/skopeo)** 是一个命令行工具，可对容器镜像和容器存储进行操作。 在没有 dockerd 的环境下，使用 **skopeo** 操作镜像是非常方便的。

```shell
yum install skopeo
```

skopeo 可以在不用下载镜像的情况下，获取镜像信息

```shell
skopeo inspect docker://docker.io/centos
```

也可以获取本地 dockerd 的镜像信息

```shell
skopeo inspect docker-daemon:ubuntu:latest
```

在不使用 docker 的情况下从远端下载镜像

```shell
# skopeo --insecure-policy copy docker://nginx:1.17.6 docker-archive:/tmp/nginx.tar
Getting image source signatures
Copying blob 8ec398bc0356 done
Copying blob 465560073b6f done
Copying blob f473f9fd0a8c done
Copying config f7bb5701a3 done
Writing manifest to image destination
Storing signatures
# ls -alh  /tmp/nginx.tar 
-rw-r--r-- 1 root root 125M 4月  13 15:22 /tmp/nginx.tar
```

也可以下载镜像到指定目录

```shell
# skopeo copy docker://busybox:latest dir:/tmp/busybox
Getting image source signatures
Copying blob 0669b0daf1fb done
Copying config 83aa35aa1c done
Writing manifest to image destination
Storing signatures

# ls -alh /tmp/busybox/
总用量 760K
drwxr-xr-x   2 root root  186 4月  13 15:26 .
drwxrwxrwt. 12 root root 4.0K 4月  13 15:25 ..
-rw-r--r--   1 root root 743K 4月  13 15:26 0669b0daf1fba90642d105f3bc2c94365c5282155a33cc65ac946347a90d90d1
-rw-r--r--   1 root root 1.5K 4月  13 15:26 83aa35aa1c79e4b6957e018da6e322bfca92bf3b4696a211b42502543c242d6f
-rw-r--r--   1 root root  527 4月  13 15:26 manifest.json
-rw-r--r--   1 root root   33 4月  13 15:25 version
```

删除镜像

```shell
skopeo delete docker://localhost:5000/nginx:latest
```



## PodMan

https://www.modb.pro/db/630149

## cri-tools



## cri-o

https://github.com/cri-o/cri-o?tab=readme-ov-file



## buildah

https://github.com/containers/buildah



## buildkit

使用**精简版 nerdctl 无法直接通过 containerd 构建镜像**，需要与 buildkit 组全使用以实现镜像构建。当然你也可以安装上面的完整 nerdctl；buildkit 项目是 Docker 公司开源出来的一个构建工具包，支持 OCI 标准的镜像构建。它主要包含以下部分:

- 服务端 buildkitd，当前支持 runc 和 containerd 作为 worker，默认是 runc；
- 客户端 buildctl，负责解析 Dockerfile，并向服务端 buildkitd 发出构建请求。

buildkit 是典型的 **C/S 架构**，client 和 server 可以不在一台服务器上。而 nerdctl 在构建镜像方面也可以作为 buildkitd 的客户端。

```shell
# https://github.com/moby/buildkit/releases
wget https://github.com/moby/buildkit/releases/download/v0.10.4/buildkit-v0.10.4.linux-amd64.tar.gz

tar -xf buildkit-v0.10.4.linux-amd64.tar.gz  -C /usr/local/
```



## OCI 与容器镜像构建

2013 年 3 月 dotCloud 公司在 PyCon 上进行了 Docker 的首次展示，随后宣布开源。自此 Docker 开始被众人知晓，随后掀起了一股[容器](https://cloud.tencent.com/product/tke?from_column=20065&from=20065)化的热潮。

在 2014 年 6 月 Docker 1.0 正式发布，有近 460 位贡献者和超过 8700 次提交，这也标志着 Docker 达到了生产可用的状态。

在当时，提到容器化第一想法就是用 Docker 。而当时 Docker 的实现或者说发展方向主要是由 Docker Inc. 公司控制的，并没有一个统一的工业标准。这对于一些头部公司而言，显然是不能接受的，没有统一的工业标准意味着如果选择了使用 Docker 的容器化技术，便会被 Docker Inc. 公司所绑定；加上随着 Docker 软件的升级，某些功能或者特性必然会进行变动，没人能保证不发生破坏性变更。

所以，为了推进容器化技术的工业标准化，2015 年 6 月在 DockerCon 上 Linux 基金会与 Google，华为，惠普，IBM，Docker，Red Hat，VMware 等公司共同宣布成立开放容器项目 (OCP)，后更名为 OCI。它的主要目标便是 建立容器格式和运行时的工业开放通用标准。

发展至今， OCI 制定的主要标准有三个分别是 `runtime-spec` 、`image-spec` 和 `distribution-spec` 这三个标准分别定义了容器运行时，容器镜像还有分发的规范

为了支持 OCI 容器运行时标准的推进，Docker 公司起草了镜像格式和运行时规范的草案，并将 Docker 项目的相关实现捐献给了 OCI 作为容器运行时的基础实现，现在项目名为 `runc` 。

后来 Docker 将其容器运行时独立成了一个项目，名为 `containerd` 并将此项目捐献给了 CNCF ，现在已经是 CNCF 毕业项目了

Docker Image

每个 Docker 镜像都是由一系列的配置清单和相应的层进行组织的。每个层一般都是 tar 格式的归档，配置清单中描述了对应的层应该按何种顺序进行组织，以及镜像的一些元属性。比如镜像所支持的架构，例如 `amd64` 之类的，还有 ENV 等提前配置好的一些参数等。

当然，在 Docker Image 中也包含着构建镜像时候所用的 Docker 版本 `docker_version` 以及构建镜像的历史记录 `history` 等信息。所以你在 `DockerHub` 或者其他的[镜像仓库](https://cloud.tencent.com/product/tcr?from_column=20065&from=20065)上可以看到构建镜像所用的 Docker 版本， 或者可通过 `docker history <IMAGE>` 的方式来查看构建历史

OCI Image

最主要的区别在于它们的目录结构不完全相同，配置信息尤其是 `mediaType` 的规范是不相同的。

而它们的联系也在于此，OCI Image 的规范是由 Docker Image 的规范修改而来的，所以类似它们的 blob 的组织形式大致是相同的，配置文件中很多的参数也相似

到目前为止，我们可以有很多种选择来构建镜像：

- BuildKit
- img
- orca-build
- umoci
- buildah
- kaniko
- FTL
- Bazel rules_docker

通常情况下，在网络上比较容易见到宣传为下一代镜像构建工具的是 `buildah` ，最主要原因是因为它可以直接构建 OCI 标准的镜像或 Docker 镜像，也可以直接使用 `Dockerfile` 。并且它还可以 `pull`/`push` 镜像，可以说在镜像构建方面与 Docker 是完全兼容，甚至可以说它在构建镜像方面可以作为 Docker 的替代品了。

并且 `buildah` 构建镜像的时候不需要任何 `root` 权限，也不依赖 Docker， 它使用了简单的 `fork-exec` 模型，同时它也可以作为一个库包含在其他的工具中。它的最终目标便是提供一个更低层次的核心工具集，来完成构建镜像相关的事情。

`img` 这个工具是构建在 `BuildKit` 之上的，所以有很多相似性。它们使用非 root 用户来构建镜像。当然 `BuildKit` 我在之前的文章中详细介绍过了，它是 Docker 内置的下一代构建工具，独立使用也是可以的。称它为 “下一代镜像构建工具” 也并不为过。

`kaniko` 是 Google 推出的，它主要的宣传语为 “在 Kubernetes 中构建容器镜像” 实际上无论是在 K8S 集群中或者在容器中它都是可以工作的。它也可以使用 `Dockerfile` 构建镜像。当然还有很重要的一点，它所有的构建命令都是运行在用户态的，并且也可以很好的与 Kubernetes 结合，在云原生时代下，它也占据了一定的优势。

## 实战



## 资源



https://www.cnblogs.com/liugp/p/16633732.html