---
###title: Docker学习
date: 2020-05-02 21:40:33
categories: IT
tags:
    - 
toc: true
thumbnail: http://cdn.kunkunzhang.top/docker.png
---

　　Docker容器部署

<!--more-->

## Docker-hub

`Docker Hub` 是 `Docker` 官方建立的中央镜像仓库，除了普通镜像仓库的功能外，它内部还有更加细致的权限管理，支持构建钩子和自动构建，并且有一套精致的 Web 操作页面

由于定位是 `Docker` 的中央镜像仓库系统，同时也是 `Docker Engine` 的默认镜像仓库，所以 `Docker Hub` 是开发者共享镜像的首选，那么也就意味着其中的镜像足够丰富

`Docker Hub` 提供了一套完整的 `Web` 操作界面，所以我们搜索其中的镜像会非常方便

`OFFICIAL`代表镜像为 `Docker` 官方提供和维护，相对来说稳定性和安全性较高

`STARS` 代表镜像的关注人数，这类似 `GitHub` 的 `Stars`，可以理解为热度

`PULLS` 代表镜像被拉取的次数，基本上能够表示镜像被使用的频度

使用docker search能够搜索docker hub中的镜像

```shell
docker search ubuntu
```



## Docker-machine





## Docker-compose

是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

docker compose的三步：

1.编写容器所需镜像的 `Dockerfile`。（如果必要）。

2.编写用于配置容器的 `docker-compose.yml`

3.使用docker-compose启动



创建docker-compose.yml文件进行配置

```yml
# yaml 配置
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

`version` 这个配置，这代表我们定义的 `docker-compose.yml` 文件内容所采用的版本，目前 `Docker Compose` 的配置文件已经迭代至了第三版，其所支持的功能也越来越丰富，所以我们建议使用最新的版本来定义。

`services`这块，是整个 `-compose.yml` 的核心部分，其定义了容器的各项细节。

`docker-compose`命令默认会识别当前控制台所在目录内的 `docker-compose.yml` 文件，而会以这个目录的名字作为组装的应用项目的名称。如果我们需要改变它们，可以通过选项 -f 来修改识别的 `Docker Compose` 配置文件，通过 -p 选项来定义项目名。

输入docker compose命令启动容器

```dockerfile
docker-compose up
```

虽然 `Docker Compose` 目前也是由 `Docker`官方主要维护，但其却不属于 `Docker Engine` 的一部分，而是一个独立的软件。所以如果我们要在 Linux 中使用它，还必须要单独下载使用。

`Docker Compose` 是一个由 `Python` 编写的软件，在拥有 `Python` 运行环境的机器上，我们可以直接运行它，不需要其它的操作。

### couldn't connect to Docker daemonat http+docker

docker没启动

先启动docker

```shell
sudo systemctl start docker
```

docker启动了但是有一些缓存影响了

重启docker

```bash
sudo systemctk restart docker
```

当前用户不在docker用户组

把自己加入docker用户组

```shell
sudo gpasswd -a ${USER} docker
```

权限问题

sudo

```shell
sudo docker-compose up
```

docker-compose版本太老



重启系统

```shell
sudo reboot
```

### dockage

在日常工作中，Docker Compose是开发者和运维人员管理容器化应用的重要工具，但管理多个`compose.yaml`文件时，手动操作难免繁琐，效率也会受到限制。

**Dockge**，拥有直观的界面和实用的功能，帮助用户更高效地管理和操作Docker Compose栈。

管理Compose文件

• 支持创建、编辑、启动、停止、重启和删除Docker Compose栈

• 提供镜像更新功能，方便维护服务的最新状态

## dive

https://github.com/wagoodman/dive

查看镜像中每一层layer占用的体积



## 数据管理

Docker虽然有很多优势，但也有很多弊端，其中显著的两点就是

沙盒文件系统是跟随容器生命周期所创建和移除的，数据无法直接被持久化存储。

由于容器隔离，我们很难从容器外部获得或操作容器内部文件中的数据

`Docker` 容器文件系统是基于 `UnionFS`。由于 `UnionFS` 支持挂载不同类型的文件系统到统一的目录结构中，所以我们只需要将宿主操作系统中，文件系统里的文件或目录挂载到容器中，便能够让容器内外共享这个文件。

基于底层存储实现，`Docker` 提供了三种适用于不同场景的文件系统挂载方式：Bind `Mount`、`Volume` 和 `Tmpfs Mount`

- `Bind Mount` 能够直接将宿主操作系统中的目录和文件挂载到容器内的文件系统中，通过指定容器外的路径和容器内的路径，就可以形成挂载映射关系，在容器内外对文件的读写，都是相互可见的。
- `Volume` 也是从宿主操作系统中挂载目录到容器内，只不过这个挂载的目录由 Docker 进行管理，我们只需要指定容器内的目录，不需要关心具体挂载到了宿主操作系统中的哪里。
- `Tmpfs Mount` 支持挂载系统内存中的一部分到容器的文件系统里，不过由于内存和容器的特征，它的存储并不是持久的，其中的内容会随着容器的停止而消失

匿名挂载

通过如下命令启动容器

```shell
docker run -d -P --name nginx01 -v /etc/nginx nginx 
```

我们在通过查看下所有数据卷的情况

```shell
docker volume ls
```

由上图可以看到，VOLUME NAME 有的是随机生成的字符串，对于这种就是匿名挂载，因为 - v 的时候只写了容器内的路径看，而没有写容器外的路径

具名挂载

```shell
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
```

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用的，不建议大家使用匿名挂载

如何确定是具名挂载还是匿名挂载，还是指定路径挂载？

-v 容器内路径 #匿名挂载

-v 卷名：容器内路径 #具名挂载

-v 主机路径：容器内路径 #指定路径挂载

### 日志

无论是为了排障还是审计的需要，后台服务总是需要日志能力。按照以往的思路，我们将日志分好类后，统一写入某个目录下的日志文件即可。但是在 Docker 中，任何本地文件都不是持久化的，会随着容器的生命周期结束而销毁。因此，我们需要将日志的存储跳出容器之外。

最简单的做法是利用 `DockerManagerVolume`，这个特性能绕过容器自身的文件系统，直接将数据写到宿主物理机器上。具体用法如下

运行 docker 时，通过-v 参数为容器绑定 volumes，将宿主机上的 `/app/log` 目录（如果没有会自动创建）挂载到容器的 `/usr/share/log` 中。这样服务在将日志写入该文件夹时，就能持久化存储在宿主机上，不随着 docker 的销毁而丢失了。

当然，当部署集群变多后，物理宿主机上的日志也会变得难以管理。此时就需要一个服务编排系统来统一管理了。从单纯管理日志的角度出发，我们可以进行网络上报，给到云日志服务（如腾讯云 CLS）托管。或者干脆将容器进行批量管理，例如 `Kubernetes`这样的容器编排系统，这样日志作为其中的一个模块自然也能得到妥善保管了。





## Docker-Swam

通常是使用 `Docker Compose` 来定义集群，而通过 `Docker Swarm` 来部署集群。

要搭建 `Overlay Network` 网络，我们就要用到 `Docker Swarm` 这个工具了。`Docker Swarm`是 `Docker`内置的集群工具，它能够帮助我们更轻松地将服务部署到 `Docker daemon` 的集群之中。

对于 `Docker Swarm`来说，每一个 `Docker daemon` 的实例都可以成为集群中的一个节点，而在 `Docker daemon` 加入到集群成为其中的一员后，集群的管理节点就能对它进行控制。我们要搭建的 `Overlay` 网络正是基于这样的集群实现的。

初始化docker-swam

既然要将 `Docker`加入到集群，我们就必须先有一个集群，我们在任意一个 `Docker`实例上都可以通过 `docker swarm init` 来初始化集群

```shell
sudo docker swarm init
```

在集群初始化后，这个 `Docker` 实例就自动成为了集群的管理节点，而其他 `Docker` 实例可以通过运行这里所打印的 `docker swarm join` 命令来加入集群。

加入到集群的节点默认为普通节点，如果要以管理节点的身份加入到集群中，我们可以通过 `ocker swarm join-token` 命令来获得管理节点的加入命令。



### 建立跨主机网络

通过 `docker network create` 命令来建立 `Overlay` 网络

```shell
 sudo docker network create --driver overlay --attachable mesh
```



在创建了这个网络之后，我们可以在任何一个加入到集群的`Docker`实例上使用 `docker network ls` 查看一下其下的网络列表。我们会发现这个网络定义已经同步到了所有集群中的节点

```shell
sudo docker network ls
```

修改 `Docker Compose` 的定义，让它使用这个我们已经定义好的网络，而不是再重新创建网络。

只需要在`Docker Compose` 配置文件的网络定义部分，将网络的 `external` 属性设置为 `true`，就可以让`Docker Compose` 将其建立的容器都连接到这个不属于 `Docker Compose` 的项目上了。



## 搭建私有docker仓库

通过官方提供的私有仓库镜像`registry`来搭建私有仓库。通过 [humpback](https://humpback.github.io/) 快速搭建轻量级的Docker容器云管理平台

拉去registry

```shell
docker pull registry:2.6.2
```

为了定制一些配置，和在 [humpback](https://humpback.github.io/) 中使用，我们还需要提供一个定制化的配置文件（使用yml来编写配置文件），文件放在`/etc/docker/registry/config.yml`，如下

```yaml
version: 0.1
log:
  fields:
    service: registry
storage: 
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :7000
  secret: docker-registry
  headers:
    X-Content-Type-Options: [nosniff]
    Access-Control-Allow-Headers: ['*']
    Access-Control-Allow-Origin: ['*']
    Access-Control-Allow-Methods: ['GET,POST,PUT,DELETE']
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
```



推送镜像到私有仓库

```shell
# 从官方仓库拉取一个镜像
docker pull nginx:1.13
# 为镜像 `nginx:1.13` 创建一个新标签 `192.168.99.100:7000/test-nginx:1.13`
docker tag nginx:1.13 192.168.99.100:7000/test-nginx:1.13
# 推送到私有仓库中
docker push 192.168.99.100:7000/test-nginx:1.13
# The push refers to a repository [192.168.99.100:7000/test-nginx]
```

部署Humpback

首先创建放持久化数据文件夹，`mkdir -p /opt/app/humpback-web`，里面存放持久化数据文件，会存储站点管理和分组信息，启动后请妥善保存。

```shell
# 创建放持久化数据文件夹
mkdir -p /opt/app/humpback-web
# 下载humpback-web镜像到本地
docker pull humpbacks/humpback-web:1.0.0
# 启动 humpback-web 容器，将容器命名为 humpback-web
docker run -d --net=host --restart=always \
 -e HUMPBACK_LISTEN_PORT=7001 \
 -v /opt/app/humpback-web/dbFiles:/humpback-web/dbFiles \
 --name humpback-web \
 humpbacks/humpback-web:1.0.0
```

访问站点，打开浏览器输入：·[http://192.168.99.100:7001·](http://192.168.99.100:7001·/) ，默认账户：`admin` 密码：`123456`

## 镜像批量清理脚本

批量删除已经推出的容器

```docker
docker ps -a | grep "Exited" | awk '{print $1 }' | xargs docker rm
```

批量删除带有none字段的镜像

```dockerfile
# 方案1： 根据镜像id删除镜像
docker images| grep none |awk '{print $3 }'|xargs docker rmi
# 方案2: 根据镜像名删除镜像
docker images | grep wecloud | awk '{print $1":"$2}' | xargs docker rmi
```

批量删除镜像定时任务脚本

```dockerfile
#!/bin/bash
# create by wangduanduan
# when current free disk less then max free disk, you can remove docker images
#
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m'
max_free_disk=5 # 5G. when current free disk less then max free disk, remove docker images
current_free_disk=`df -lh | grep centos-root | awk '{print strtonum($4)}'`
df -lh
echo "max_free_disk: $max_free_disk G"
echo -e "current_free_disk: ${GREEN} $current_free_disk G ${NC}"
if [ $current_free_disk -lt $max_free_disk ]
then
	echo -e "${RED} need to clean up docker images ${NC}"
	docker images | grep none | awk '{print $3 }' | xargs docker rmi
	docker images | grep wecloud | awk '{print $1":"$2}' | xargs docker rmi
else
	echo -e "${GREEN}no need clean${NC}"
fi
```

注意：`为了加快打包的速度，一般不要太频繁的删除镜像`。因为老的镜像中的某些不改变的层，可以作为新的镜像的缓存，从而大大加快构建的速度。

## 实战1：部署mysql和nginx

部署nginx

步骤：

1.在docker hub中查找nginx镜像

```shell
docker search nginx
```

2.拉取官方镜像

```shell
docker pull nginx
```

3.利用镜像启动容器

```shell
docker run --name my-nginx -d -p 8080:80 nginx
```

`--name`:为容器取名  -d：后台运行。-p：映射端口，-p host port:container port 

4.查看容器运行日志

```shell
docker logs my-nginx
```

部署mysql

步骤

拉取mysql镜像文件

```shell
docker pull mysql:5.7
```

创建目录

- `data`目录将映射为`mysql`容器配置的数据文件存放路径

- `logs`目录将映射为`mysql`容器的日志目录
- `conf`目录里的配置文件将映射为`mysql`容器的配置文件

```shell
mkdir -p ~/mysql/data ~/mysql/logs ~/mysql/conf
```

运行容器

```shell
docker run --name my-mysql \ 
-p 3306:3306 \ 
-v $PWD/conf/my.cnf:/etc/mysql/my.cnf \ 
-v $PWD/logs:/logs \ 
-v $PWD/data:/mysql_data \ 
-e MYSQL_ROOT_PASSWORD=123456 \ 
-d mysql:5.7
```

`-p 3306:3306`：将容器的`3306`端口映射到主机的`3306`端口

`-v $PWD/conf/my.cnf:/etc/mysql/my.cnf`：将主机当前目录下的`conf/my.cnf`挂载到容器的`/etc/mysql/my.cnf`

`-v $PWD/logs:/logs`：将主机当前目录下的`logs`目录挂载到容器的`/logs`

`-v $PWD/data:/mysql_data`：将主机当前目录下的`data`目录挂载到容器的`/mysql_data`

`-e MYSQL_ROOT_PASSWORD=123456`：初始化root用户的密码



## 实战2:部署php、mysql、nginx



## colima

https://github.com/abiosoft/colima?tab=readme-ov-file

## 源

https://cloudlayer.icu/

https://github.com/DaoCloud/public-image-mirror



## docker中文资源

- Docker中文网站：[http://www.docker.org.cn](http://www.docker.org.cn/)
- Docker中文文档：http://www.dockerinfo.net/document
- Docker安装手册：http://www.docker.org.cn/book/install.html
- 一小时Docker教程 ：https://blog.csphere.cn/archives/22
- Docker中文指南：http://www.widuu.com/chinese_docker/index.html

Docker官方资源

- Docker官网：[http://www.docker.com](http://www.docker.com/)
- Docker windows入门：https://docs.docker.com/windows/
- Docker Linux 入门：https://docs.docker.com/linux/
- Docker mac 入门：https://docs.docker.com/mac/
- Docker 用户指引：https://docs.docker.com/engine/userguide/
- Docker 官方博客：http://blog.docker.com/
- Docker Hub: https://hub.docker.com/
- Docker开源： https://www.docker.com/open-source

其他资源

- [Docker 快速手册！](https://github.com/eon01/DockerCheatSheet)
- [Docker 教程](http://www.runoob.com/docker/docker-tutorial.html)
- [MySQL Docker 单一机器上如何配置自动备份](http://blog.csdn.net/zhangchao19890805/article/details/52756865)
- https://segmentfault.com/t/docker
- https://github.com/docker/docker
- https://wiki.openstack.org/wiki/Docker
- https://wiki.archlinux.org/index.php/Docker

https://docs.ffffee.com/docker/docker-%E7%89%88%E6%9C%AC%E5%90%8E%E7%BC%80.html