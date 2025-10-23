---
title: Linux3
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - IT，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OI3V.th.jpg
---

​     服务器配置

<!--more-->

## caddy

Caddy 是一个 Go 编写的 Web 服务器，类似于 Nginx，Caddy 提供了更加强大的功能，随着 v2 版本发布 Caddy 已经可以作为中小型站点 Web 服务器的另一个选择；相较于 Nginx 来说使用 Caddy 的优势如下:

- 自动的 HTTPS 证书申请(ACME HTTP/DNS 挑战)
- 自动证书续期以及 OCSP stapling 等
- 更高的安全性包括但不限于 TLS 配置以及内存安全等
- 友好且强大的配置文件支持
- 支持 API 动态调整配置(有木有人可以搞个 Dashboard？)
- 支持 HTTP3(QUIC)
- 支持动态后端，例如连接 Consul、作为 k8s ingress 等
- 后端多种负载策略以及健康检测等
- 本身 Go 编写，高度模块化的系统方便扩展(CoreDNS 基于 Caddy1 开发)
- ……

一键安装

```shell
curl https://getcaddy.com | bash -s personal
或者
wget -qO- https://getcaddy.com | bash -s personal
```

检查安装版本

```bash
which caddy
```

## Tengine

nginx加强版

https://github.com/alibaba/tengine

https://tengine.taobao.org/documentation_cn.html



## Harbor

Harbor是VMware公司开源的一个用于存储和分发Docker镜像的企业级Registry服务器，以Docker开源的Registry为基础，通过添加一些企业必需的功能特性，例如安全、标识和管理等，扩展了开源Docker Distribution。作为一个企业级私有Registry服务器，Harbor提供了更好的性能和安全，提升用户使用Registry构建和运行环境传输镜像的效率。Harbor支持安装在多个Registry节点的镜像资源复制，镜像全部保存在私有Registry中，确保数据和知识产权在公司内部网络中管控。另外，Harbor也提供了高级的安全特性，诸如用户管理，访问控制和活动审计等。

Harbor特性

- 基于角色的访问控制（Role Based Access Control）

- 基于策略的镜像复制（Policy based image replication）

- 镜像的漏洞扫描（Vulnerability Scanning）

- AD/LDAP集成（LDAP/AD support）

- 镜像的删除和空间清理（Image deletion & garbage collection）

- 友好的管理UI（Graphical user portal）

- 审计日志（Audit logging）

- RESTful API

- 部署简单（Easy deployment）

Harbor组件：

（1）依赖的外部组件:

- Nginx（Proxy）: Harbor的Registry、UI、Token等服务，通过一个前置的反向代理统一接收浏览器、Docker客户端的请求，并将请求转发给后端不同的服务。

- Registry v2: Docker官方镜像仓库, 负责储存Docker镜像，并处理Docker Push/Pull命令。由于我们要对用户进行访问控制，即不同用户对Docker镜像有不同的读写权限，Registry会指向一个Token服务，强制用户的每次Docker Push/Pull请求都要携带一个合法的Token, Registry会通过公钥对Token进行解密验证。

- Database（MySQL/Postgresql）：为Core Services提供数据库服务，负责储存用户权限、审计日志、Docker镜像分组信息等数据。

（2）Harbor自己的组件

Core Services（Admin Server）: 这是Harbor的核心功能，主要提供以下服务：

API：提供Harbor RESTful API

UI：提供图形化界面，帮助用户管理Registry上的镜像, 并对用户进行授权。

Webhook：为了及时获取Registry上镜像状态变化的情况，在Registry上配置Webhook，把状态变化传递给UI模块。

Auth服务：负责根据用户权限给每个Docker Push/Pull命令签发Token。Docker客户端向Registry服务发起的请求，如果不包含Token，会被重定向到这里，获得Token后再重新向Registry进行请求。

Replication Job Service：提供多个Harbor实例之间的镜像同步功能。

Log Collector：为了帮助监控Harbor运行，负责收集其他组件的日志，供日后进行分析。

### 安装

不建议使用Kubernetes来安裝，原因是镜像仓库非常重要，尽量保证安裝和维护的简洁性，因此这里直接使用Docker Compose的方式进行安裝。事实上Harbor的每个组件都是以Docker容器的形式构建，官方也是使用Docker Compose来对它进行安裝。Harbor官方提供以下三种安裝方式:

在线安装：从Docker Hub下载Harbor的镜像来安装，由于Docker Hub比较慢，建议Docker配置好加速器。

离线安装：这种方式应对与安裝主机没联网的情况使用，需要提前下载离线安装包到本地。

OVA安装：这个主要用vCentor环境时使用。

```shell
# 安装方式分为在线安装和离线安装两种方式，这里采用在线安装方式

# 下载在线安装程序
wget -P /usr/local https://storage.googleapis.com/harbor-releases/release-1.7.0/harbor-online-installer-v1.7.1.tgz

# 解压下载文件
tar zxf /usr/local/harbor-online-installer-v1.7.1.tgz -C /usr/local/

# 修改配置文件，根据自己的需求进行修改
vim /usr/local/harbor/harbor.cfg
# 本机IP或者域名，不能是127.0.0.1或者localhost
hostname = 192.168.1.130
# 系统Harbor管理员的密码
harbor_admin_password = Harbor12345
# 禁止用户注册
self_registration = off
# 设置只有管理员可以创建项目
project_creation_restriction = adminonly

# 由于Harbor的Nginx组件默认会监听宿主机的80、443、4443端口，如果需要更改Nginx的端口映射，可以修改以下配置文件
vim /usr/local/harbor/docker-compose.yml
 ports:
      - 8082:80
      - 443:443
      - 4443:4443

# 如果上面更改了Nginx的80端口映射，此时还需要编辑Harbor的配置文件，修改hostname加上指定的端口号
# vim harbor.cfg
hostname = 192.168.1.130:8082

# 执行安装脚本
/usr/local/harbor/install.sh

# Harbar的日志目录是：/var/log/harbor
# Harbar相关数据卷的挂载目录默认是宿主机的/data目录，如果重新安装Harbar并在配置文件里更改了数据库密码，则需要删除/data目录，否则Harbor部分组件会启动失败
```

Harbor启动/停止/重启

```shell
# 如果某个Harbor组件启动失败，可以在日志目录/var/log/harbor下查看具体的日志信息，进一步定位启动失败的原因
# 启动时Harbor默认会监听宿主机的80、443、4443端口，启动Harbor之前必须确保宿主机的80、443、4443端口不被占用，否则Harbor相关组件会启动失败。

# 查看Harbor容器的运行状态
docker ps

# 或者通过docker-compose查看，此时需要进入Harbor安装脚本所在的目录里执行相关命令
cd /usr/local/harbor

# 查看Harbor容器的运行状态
docker-compose ps
       Name                     Command                  State                                    Ports
-------------------------------------------------------------------------------------------------------------------------------------
harbor-adminserver   /harbor/start.sh                 Up (healthy)
harbor-core          /harbor/start.sh                 Up (healthy)
harbor-db            /entrypoint.sh postgres          Up (healthy)   5432/tcp
harbor-jobservice    /harbor/start.sh                 Up
harbor-log           /bin/sh -c /usr/local/bin/ ...   Up (healthy)   127.0.0.1:1514->10514/tcp
harbor-portal        nginx -g daemon off;             Up (healthy)   80/tcp
nginx                nginx -g daemon off;             Up (healthy)   0.0.0.0:443->443/tcp, 0.0.0.0:4443->4443/tcp, 0.0.0.0:80->80/tcp
redis                docker-entrypoint.sh redis ...   Up             6379/tcp
registry             /entrypoint.sh /etc/regist ...   Up (healthy)   5000/tcp
registryctl          /harbor/start.sh                 Up (healthy)

# 启动Harbor容器
docker-compose start

# 停止Harbor容器
docker-compose stop

# 重启Harbor容器
docker-compose restart

# 停止并删除Harbor容器，加上-v参数可以同时移除挂载在容器上的目录
docker-compose down

# 创建并启动Harbo容器，参数“-d”表示后台运行命令
docker-compose up -d
```

测试访问harbor

浏览器输入以下地址或者域名访问Harbor的Web界面，默认账号密码：admin/Harbor12345 地址： http://192.168.1.130

把本地镜像push到harbor

```shell
# 配置Docker客户端允许使用Http协议，如果Nginx更改了的端口映射，需要在以下IP地址后面指定具体的端口号
vim /etc/docker/daemon.json
{
  "insecure-registries":["192.168.1.130"]
}

# 重新加载Docker的配置文件
systemctl daemon-reload

# 重启Docker
systemctl restart docker

# 拉取Docker官方的Centos镜像
docker pull centos:latest

# 查看镜像列表
docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
centos                        latest              1e1148e4cc2c        7 weeks ago         202MB
....

# 登录Harbor Registry，回车后输入admin用户的帐号信息（admin/Harbor12345）
docker login 192.168.1.130

# 如果不使用默认项目名library，则需要使用admin用户提前登录Harbor的Web界面，手动创建新项目后再进行Push操作
# 给镜像打上相应的标签, 注意标签格式: ip/{project-name}/{image-name}[:tag]
# 项目library只有admin有写的权限
docker tag centos:latest 192.168.1.130/library/centos:1.0

# 将本地镜像Push到Harbor
docker push 192.168.1.130/library/centos:1.0
```

把harbor镜像pull到本地

```shell
# 删除上面创建的镜像
docker rmi centos
docker rm 192.168.1.130/library/centos:1.0

# 将Harbor镜像Pull到本地
docker pull 192.168.1.130/library/centos:1.0

# 查看镜像列表
docker ps
```

Harbor安装后更改Nginx的端口映射

```shell
# 进入Harbor的安装目录
cd /usr/local/harbor

# 停止并删除Harbor容器，加上-v参数可以同时移除挂载在容器上的目录
docker-compose down

# 编辑compose的配置文件，修改Nginx的80端口映射
vim docker-compose.yml
 ports:
      - 8082:80
      - 443:443
      - 4443:4443

# 编辑Harbor的配置文件，修改hostname加上指定的端口号
vim harbor.cfg
hostname = 192.168.1.130:8082

# 重新生成配置文件
prepare

# 创建并启动Harbor容器
docker-compose up -d

# 查看Harbor的容器列表，发现Nginx的端口映射已经更改成功
docker-compose ps
harbor-adminserver   /harbor/start.sh                 Up (health: starting)
harbor-core          /harbor/start.sh                 Up (health: starting)
harbor-db            /entrypoint.sh postgres          Up (health: starting)   5432/tcp
harbor-jobservice    /harbor/start.sh                 Up
harbor-log           /bin/sh -c /usr/local/bin/ ...   Up (healthy)            127.0.0.1:1514->10514/tcp
harbor-portal        nginx -g daemon off;             Up (health: starting)   80/tcp
nginx                nginx -g daemon off;             Up (health: starting)   0.0.0.0:443->443/tcp, 0.0.0.0:4443->4443/tcp, 0.0.0.0:8082->80/tcp
redis                docker-entrypoint.sh redis ...   Up                      6379/tcp
registry             /entrypoint.sh /etc/regist ...   Up (health: starting)   5000/tcp
registryctl          /harbor/start.sh                 Up (health: starting)
```

使用openssl生成TLS证书，用于Harbor配置Https

```shell
# 下面以IP：192.168.1.130为例子，实际操作中将命令中的IP地址修改为自己的IP地址即可

# 创建存放证书的临时目录
mkdir ~/cert
cd ~/cert

# 创建自签名根证书
openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout ca.key \
    -x509 -days 1000 -out ca.crt \
    -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=test_company/OU=IT/CN=test/emailAddress=test@qq.com"

# ls
ca.crt  ca.key

# 产生证书签名请求
openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout harbor-registry.key \
    -out harbor-registry.csr \
    -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=test_company/OU=IT/CN=192.168.1.130/emailAddress=test@qq.com"

# ls
ca.crt  ca.key  harbor-registry.csr  harbor-registry.key

# 为Registry主机产生证书
echo subjectAltName = IP:192.168.1.130 > extfile.cnf
openssl x509 -req -days 1000 -in harbor-registry.csr -CA ca.crt -CAkey ca.key -CAcreateserial -extfile extfile.cnf -out harbor-registry.crt

# ls
ca.crt  ca.key  ca.srl  extfile.cnf  harbor-registry.crt  harbor-registry.csr  harbor-registry.key

# 创建Harbor的证书目录
mkdir -p /opt/cert

# 拷贝harbor-registry证书到Harbor的证书目录
cp harbor-registry.crt /opt/cert/
cp harbor-registry.key /opt/cert/
```

Harbor安装后配置https

```shell
# 进入Harbor的安装目录
cd /usr/local/harbor

# 停止并删除Harbor容器，加上-v参数可以同时移除挂载在容器上的目录
docker-compose down

# 修改harbor.cfg配置文件
# vim /usr/local/harbor/harbor.cfg
ui_url_protocol = https
hostname = 192.168.1.130
ssl_cert = /opt/cert/harbor-registry.crt
ssl_cert_key = /opt/cert/harbor-registry.key

# 重新生成配置文件
prepare

# 让Docker客户端默认使用Https协议访问Registry，需要去掉“insecure-registries”相关配置项
# 查看daemon.json文件中是否有"insecure-registries":["192.168.1.130"]，如果有则将其删除掉
vim /etc/docker/daemon.json
{"insecure-registries":[""]}

# 重新加载Docker的配置文件
systemctl daemon-reload

# 重启Docker
systemctl restart docker

# 创建并启动Harbor容器
docker-compose up -d
```

测试 

通过https协议访问harbor

这里首先需要将上面产生的~/cert/ca.crt导入到浏览器的受信任的根证书中，然后就可以通过Https协议访问Harbor的Web界面了，但不能保证所有浏览器都支持。访问地址是： https://192.168.1.130

然后通过docker命令来访问

```shell
# 创建Docker的证书目录，目录名称是IP地址，需要根据自己的情况进行修改
# 注意，如果Nginx的443端口映射到了其他端口，则目录名称需要带上具体的端口号，例如/etc/docker/certs.d/192.168.1.130:8443
mkdir -p /etc/docker/certs.d/192.168.1.130

# 将上面产生的ca.crt拷贝到Docker的证书目录下
cp ~/cert/ca.crt /etc/docker/certs.d/192.168.1.130

# 重启Docker
systemctl restart docker

# 登录Harbor Registry，回车后输入admin用户的帐号信息（admin/Harbor12345）
docker login 192.168.1.130

# 查看镜像列表
# docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
centos                        latest              1e1148e4cc2c        7 weeks ago         202MB
....

# 给镜像打上标签
docker tag centos:latest 192.168.1.130/library/centos:1.0

# 将本地镜像Push到Harbor
docker push 192.168.1.130/library/centos:1.0
```



Docker搭建私有仓库管理系统Harbor
https://blog.51cto.com/wutengfei/2480749



## devbox

像容器一样使用终端

安装devbox

```shell
curl -fsSL https://get.jetpack.io/devbox | bash
```

初始化

```shell
devbox init
```

添加Nix Packages

```shell
devbox 310 python310
```

可以把devbox的配置写入项目中，或者全局使用

全局添加项目

```shell
devbox global add ripgrep vim git
```

查看全局项目

```shell
devbox global list
```

可以运行命令，将devbox的环境同步到目前的shell中

```shell
. <(devbox global shellenv)
```

也可以添加默认配置，每次打开shell都使用

在`~/.bashrc` 或者`~/.zshrc`中添加

```bash
eval "$(devbox global shellenv)"
```

从json文件中加载配置

```shell
# Load the global config from a file

devbox global pull /path/to/devbox.json

# Load the global config from a github repo

devbox global pull https://raw.githubusercontent.com/org/repo/branch/path/to/devbox.json
```

在项目中添加devbox.json

```json
"nixpkgs": {
    "commit": "89f196fe781c53cb50fef61d3063fa5e8d61b6e5"
}
```

在devbox.json中添加脚本

```json
"shell": {
    "init_hook": "echo \"Hello \"",
    "scripts": {
        "echo_once": "echo \"World\"", 
        "echo_twice": [
            "echo \"World\"",
            "echo \"Again\""
        ]
    }
}
```

查看项目所有项目

```shell
devbox services ls
```

启动项目所有服务

```shell
devbox services start
```

### github  action

devbox的action `devbox-install-action`

在github action的yaml中添加

```yaml
- name: Install devbox
  uses: jetpack-io/devbox-install-action@v0.2.0
```

完整的action文件示例

```yaml
name: Testing with devbox

on: push

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Install devbox
        uses: jetpack-io/devbox-install-action@v0.2.0

      - name: Run arbitrary commands
        run: devbox shell -- echo "done!"

      - name: Run a script called test
        run: devbox run test
```



## sudo-rs

https://github.com/memorysafety/sudo-rs



## ext与xfs文件系统

**EXT2** **第二代扩展文件系统**（[英语](http://baike.baidu.com/item/英语)：second extended filesystem，缩写为 ext2），是 [LINUX](http://baike.baidu.com/item/LINUX) 内核所用的 [文件系统](http://baike.baidu.com/item/文件系统)。它开始由 Rémy Card 设计，用以代替 [ext](http://baike.baidu.com/item/ext)，于 1993 年 1 月加入 linux 核心支持之中。ext2 的经典实现为 LINUX 内核中的 ext2fs 文件系统驱动，最大可支持 2TB 的文件系统，至 linux 核心 2.6 版时，扩展到可支持 32TB。其他的实现包括 GNU Hurd，[Mac OS X](http://baike.baidu.com/item/Mac OS X) (第 3 方)，[Darwin](http://baike.baidu.com/item/Darwin) (第 3 方)，[BSD](http://baike.baidu.com/item/BSD)。ext2 为数个 LINUX 发行版的默认文件系统，如 [Debian](http://baike.baidu.com/item/Debian)、[Red Hat Linux](http://baike.baidu.com/item/Red Hat Linux) 等。   

​    其单一文件大小与文件系统本身的容量上限与文件系统本身的簇大小有关，在一般常见的 x86 [电脑系统](http://baike.baidu.com/item/电脑系统)中，簇最大为 4KB, 则单一文件大小上限为 2048GB, 而文件系统的容量上限为 16384GB。

​    但由于目前核心 2.4 所能使用的单一分割区最大只有 2048GB，实际上能使用的文件系统[容量](http://baike.baidu.com/item/容量)最多也只有 2048GB。

​    至于 [Ext3](http://baike.baidu.com/item/Ext3) 文件系统，它属于一种日志文件系统，是对 ext2 系统的扩展。它兼容 ext2，并且从 ext2 转换成 [ext3](http://baike.baidu.com/item/ext3) 并不复杂。

特点：

1、当创建 Ext2 文件系统时，系统管理员可以根据预期的文件平均长度来选择最佳的块大小（从 1024B——4096B）。例如，当文件的平均长度小于几千字节时，块的大小为 1024B 是最佳的，因为这会产生较少的内部碎片 —— 也就是文件长度与存放块的磁盘分区有较少的不匹配。另一方面，大的块对于 大于几千字节的文件通常比较合合适，因为这样的磁盘传送较少，因而减轻了系统的开销.

2、当创建 Ext2 文件系统时，系统管理员可以根据在给定大小的分区上预计存放的文件数来选择给该分区分配多少个索引节点。这可以有效地利用磁盘的空间。

3、文件系统把磁盘块分为组。每组包含存放在相邻磁道上的数据块和索引节点。正是这种结构，使得可以用较少的磁盘平均寻道时间对存放在一个单独块组中的文件并行访问。

4、在磁盘数据块被实际使用之前，文件系统就把这些块预分配给普通文件。因此当文件的大小增加时，因为物理上相邻的几个块已被保留，这就减少了文件的碎片。

5、支持快速符号链接。如果符号链接表示一个短路径名（小于或等于 60 个字符），就把它存放在索引节点中而不用通过由一个数据块进行转换。

Ext2 还包含了一些使它既健壮又灵活的特点：

1、文件更新策略的谨慎实现将系统崩溃的影响减到最少。我们只举一个例子来体现这个优点：例如，当给文件创建一个硬链接时，首先增加磁盘索引节点中 的硬链接计数器，然后把这个新的名字加到合适的目录中。在这种方式下，如果在更新索引节点后而改变这个目录之前出现一个硬件故障，这样即使索引节点的计数 器产生错误，但目录是一致的。因此，尽管删除文件时无法自动收回文件的数据块，但并不导致灾难性的后果。如果这种处理的顺序相反（更新索引节点前改变目 录），同样的硬件故障将会导致危险的不一致，删除原始的硬链接就会从磁盘删除它的数据块，但新的目录项将指向一个不存在的索引节点。如果那个索引节点号以 后又被另外的文件所使用，那么向这个旧目录的写操作将毁坏这个新的文件。

2、在启动时支持对文件系统的状态进行自动的一致性检查。这种检查是由外部程序 e2fsck 完成的，这个外部程序不仅可以在系统崩溃之后被激活，也 可以在一个预定义的文件系统安装数（每次安装操作之后对计数器加 1）之后被激活，或者在自从最近检查以来所花的预定义时间之后被激活。

3、支持不可变（immutable）的文件（不能修改、删除和更名）和仅追加（append-only）的文件（只能把数据追加在文件尾）。

4、既与 Unix System V Release 4（SVR4）相兼容，也与新文件的用户组 ID 的 BSD 语义相兼容。在 SVR4 中，新文件采用创建它的进程的用户组 ID；而在 BSD 中，新文件继承包含它 的目录的用户组 ID。Ext2 包含一个安装选项，由你指定采用哪种语义。

即使 Ext2 文件系统是如此成熟、稳定的程序，也还要考虑引入另外几个负面特性。目前，一些负面特性已新的文件系统或外部补丁避免了。另外一些还仅仅处于计划阶段，但在一些情况下，已经在 Ext2 的索引节点中为这些特性引入新的字段。

**最重要的一些特点如下：**

  **块片（block fragmentation）**

​    系统管理员对磁盘的访问通常选择较大的块，因为计算机应用程序常常处理大文件。因此，在大块上存放小文件就会浪费很多磁盘空间。这个问题可以通过把几个文件存放在同一块的不同片上来解决。

透明地处理压缩和加密文件

这些新的选项（创建一个文件时必须指定）将允许用户透明地在磁盘上存放压缩和（或）加密的文件版本。

  **逻辑删除**

​    一个 undelete 选项将允许用户在必要时很容易恢复以前已删除的文件内容。

  **日志**

​    日志避免文件系统在被突然卸载（例如，作为系统崩溃的后果）时对其自动进行的耗时检查。

​    实际上，这些特点没有一个正式地包含在 Ext2 文件系统中。有人可能说 Ext2 是这种成功的牺牲品；直到几年前，它仍然是大多数 Linux 发布公司采用的首选文件系统，每天有成千上万的用户在使用它，这些用户会对用其他文件系统来代替 Ext2 的任何企图产生质疑。

​    Ext2 中缺少的最突出的功能就是日志，日志是高可用服务器必需的功能。为了平顺过渡，日志没有引入到 Ext2 文件系统；但是，我们在后面 “Ext3 文件系统” 中会讨论，完全与 Ext2 兼容的一种新文件系统已经创建，这种文件系统提供了日志。不真正需要日志的用户可以继续使用良好而老式的 Ext2 文件系统，而其他用户可能采用这种新的文件系统。现在发行的大部分系统采用 Ext3 作为标准的文件系统。

**Ext2 文件系统格式**

  The Second Extended File System (ext2) 文件系统是 Linux 系统中的标准文件系统，是通过对 Minix 的文件系统进行扩展而得到的，其存取文件的性能极好。

  在 ext2 文件系统中，文件由 inode（包含有文件的所有信息）进行唯一标识。一个文件可能对应多个文件名，只有在所有文件名都被删除后，该文件才会被删除。此外，同一文件在[磁盘](http://baike.baidu.com/item/磁盘)中存放和被打开时所对应的 inode 是不同的，并由[内核](http://baike.baidu.com/item/内核)负责同步。

  ext2 文件系统采用三级间接块来存储[数据块](http://baike.baidu.com/item/数据块)[指针](http://baike.baidu.com/item/指针)，并以块（block，默认为 1KB）为单位分配空间。其磁盘分配策略是尽可能将逻辑相邻的文件分配到磁盘上物理相邻的块中，并尽可能将碎片分配给尽量少的文件，以从全局上提高性能。ext2 文件系统将同一目录下的文件（包括目录）尽可能的放在同一个块组中，但目录则分布在各个块组中以实现[负载均衡](http://baike.baidu.com/item/负载均衡)。在扩展文件时，会尽量一次性扩展 8 个连续块给文件（以预留空间的形式实现）。

   **ext3 和 ext4 的最大区别在于**，ext3 在 fsck 时需要耗费大量时间（文件越多，时间越长），而 ext4 在 fsck 时用的时间会少非常多

​    **ext4 是第四代扩展文件系统**（英语：Fourth EXtended filesystem，缩写为 ext4）是 linux 系统下的日志文件系统，是 ext3 文件系统的后继版本
​    ext4 的文件系统容量达到 1EB，而文件容量则达到 16TB，这是一个非常大的数字了。对一般的台式机和服务器而言，这可能并不重要，但对于大型磁盘阵列的用户而言，这就非常重要了。
​    ext3 目前只支持 32000 个子目录，而 ext4 取消了这一限制，理论上支持无限数量的子目录

xfs文件系统简介

xfs 是一种非常优秀的日志文件系统，它是 SGI 公司设计的。xfs 被称为业界最先进的、最具可升级性的文件系统技术
    xfs 是一个 64 位文件系统，最大支持 8EB 减 1 字节的单个文件系统，实际部署时取决于宿主操作系统的最大块限制。对于一个 32 位 Linux 系统，文件和文件系统的大小会被限制在 16TB
    xfs 在很多方面确实做的比 ext4 好，ext4 受限制于磁盘结构和兼容问题，可扩展性和 scalability 确实不如 xfs，另外 xfs 经过很多年发展，各种锁的细化做的也比较好

​    XfS 文件系统是 SGI 开发的高级日志文件系统，XFS 极具伸缩性，非常健壮。所幸的是 SGI 将其移植到了 Linux 系统中。在 linux 环境下。目前版本可用的最新 XFS 文件系统的为 1.2 版本，可以很好地工作在 2.4 核心下。

**特性：**

  主要特性包括以下几点：

  **数据完全性**

  采用 XFS 文件系统，当意想不到的宕机发生后，首先，由于文件系统开启了日志功能，所以你磁盘上的文件不再会意外宕机而遭到破坏了。不论目前文件系统上存储的文件与数据有多少，文件系统都可以  根据所记录的日志在很短的时间内迅速恢复磁盘文件内容。

  **传输特性**

  XFS 文件系统采用优化算法，日志记录对整体文件操作影响非常小。XFS 查询与分配存储空间非常快。xfs 文件系统能连续提供快速的反应时间。笔者曾经对 XFS、JFS、Ext3、ReiserFS 文件系统进行过测试，XFS 文件文件系统的性能表现相当出众。

  **可扩展性**

  XFS 是一个全 64-bit 的文件系统，它可以支持上百万 T 字节的存储空间。对特大文件及小尺寸文件的支持都表现出众，支持特大数量的目录。最大可支持的文件大 小为 263 = 9 x 1018 = 9 exabytes，最大文件系统尺寸为 18 exabytes。

XFS 使用高的表结构 (B + 树)，保证了文件系统可以快速搜索与快速空间分配。XFS 能够持续提供高速操作，文件系统的性能不受目录中目录及文件数量的限制。

  **传输带宽**

  XFS 能以接近裸设备 I/O 的性能存储数据。在单个文件系统的测试中，其吞吐量最高可达 7GB 每秒，对单个文件的读写操作，其吞吐量可达 4GB 每秒。

规范

  **容量**

XFS 是一个 [64 位](http://baike.baidu.com/item/64位)文件系统，最大支持 8exbibytes 减 1 字节的单个文件系统，实际部署时取决于宿主操作系统的最大块限制。对于一个 [32 位](http://baike.baidu.com/item/32位) Linux 系统，文件和文件系统的大小会被限制在 16tebibytes。

**
**

  **文件系统日志**

  日志文件系统是一种即使在断电或者是操作系统崩溃的情况下保证文件系统一致性的途径。XFS 对文件系统[元数据](http://baike.baidu.com/item/元数据)提供了日志支持。当文件系统更新时，[元数据](http://baike.baidu.com/item/元数据)会在实际的磁盘块被更新之前顺序写入日志。XFS 的日志被保存在[磁盘](http://baike.baidu.com/item/磁盘)块的循环缓冲区上，不会被正常的文件系统操作影响。XFS 日志大小的上限是 64k 个块和 128MB 中的较大值，下限取决于已存在的文件系统和目录的块的大小。在外置设备上部署日志会浪费超过最大日志大小的空间。XFS 日志也可以被存在文件系统的数据区（称为内置日志），或者一个额外的设备上（以减少磁盘操作）。

XFS 的[日志](http://baike.baidu.com/item/日志)保存的是在更高层次上描述已进行的操作的 “逻辑” 实体。相比之下，“物理” [日志](http://baike.baidu.com/item/日志)存储每次[事务](http://baike.baidu.com/item/事务)中被修改的块。为了保证性能，[日志](http://baike.baidu.com/item/日志)的更新是异步进行的。当[系统崩溃](http://baike.baidu.com/item/系统崩溃)时，崩溃的一瞬间之前所进行的所有操作可以利用[日志](http://baike.baidu.com/item/日志)中的数据重做，这使得 XFS 能保持文件系统的一致性。XFS 在挂载文件系统的同时进行恢复，恢复速度与文件系统的大小无关。对于最近被修改但未完全写入[磁盘](http://baike.baidu.com/item/磁盘)的数据，XFS 保证在重启时清零所有未被写入的[数据块](http://baike.baidu.com/item/数据块)，以防止任何有可能的、由剩余数据导致的安全隐患（因为虽然从文件系统接口无法访问这些数据，但不排除[裸设备](http://baike.baidu.com/item/裸设备)或裸硬件被直接读取的可能性）。

**分配组**

​    XFS 文件系统内部被分为多个 “分配组”，它们是文件系统中的等长线性存储区。每个分配组各自管理自己的 [inode](http://baike.baidu.com/item/inode) 和剩余空间。文件和文件夹可以跨越分配组。这一机制为 XFS 提供了可伸缩性和并行特性 —— 多个[线程](http://baike.baidu.com/item/线程)和进程可以同时在同一个文件系统上执行 I/O 操作。这种由分配组带来的内部分区机制在一个文件系统跨越多个[物理设备](http://baike.baidu.com/item/物理设备)时特别有用，使得优化对下级存储部件的[吞吐量](http://baike.baidu.com/item/吞吐量)利用率成为可能。

**原生备份 / 恢复工具**

  XFS 提供了 xfsdump 和 xfsrestore 工具协助备份 XFS 文件系统中的数据。xfsdump 按 [inode](http://baike.baidu.com/item/inode) 顺序备份一个 XFS 文件系统。与传统的 UNIX 文件系统不同，XFS 不需要在 dump 前被卸载；对使用中的 XFS 文件系统做 dump 就可以保证镜像的一致性。这与 XFS 对快照的实现不同，XFS 的 dump 和 restore 的过程是可以被中断然后继续的，无须冻结文件系统。xfsdump 甚至提供了高性能的多线程备份操作 —— 它把一次 dump 拆分成多个数据流，每个数据流可以被发往不同的目的地。不过到目前为止，Linux 尚未完成对多数据流 dump 功能的完整移植。

**原子磁盘配额**

  XFS 的磁盘配额在文件系统被初次挂载时启用。这解决了一个在其它大多数文件系统中存在的一个竞争问题：要求先挂载文件系统，但直到调用 quotaon (8) 之前配额不会生效。

**性能考虑**  :

 写入屏障

  XFS 文件系统默认在挂载时启用 “写入屏障” 的支持。该特性会一个合适的时间冲刷下级存储设备的写回缓存，特别是在 XFS 做日志写入操作的时候。这个特性的初衷是保证文件系统的一致性，具体实现却因设备而异 —— 不是所有的下级硬件都支持[缓存](http://baike.baidu.com/item/缓存)冲刷请求。在带有电池供电缓存的硬件 RAID 控制器提供的逻辑设备上部署 XFS 文件系统时，这项特性可能导致明显的性能退化，因为文件系统的代码无法得知这种缓存是非易失性的。如果该控制器又实现了冲刷请求，数据将被不必要地频繁写入[物理磁盘](http://baike.baidu.com/item/物理磁盘)。为了防止这种问题，对于能够在断电或发生其它主机[故障](http://baike.baidu.com/item/故障)时保护缓存中数据的设备，应该以 nobarrier 选项挂载 XFS 文件系统。

**日志的放置**

  XFS 文件系统创建时默认使用内置[日志](http://baike.baidu.com/item/日志)，把日志和文件系统数据放置在同一个[块设备](http://baike.baidu.com/item/块设备)上。由于在所有的文件系统写入发生前都要更新日志中的[元数据](http://baike.baidu.com/item/元数据)，内置日志可能导致磁盘竞争。在大多数负载下，这种等级的竞争非常低以至于对性能没有影响。但对于沉重的随机写入负载，比如在忙碌的[数据块](http://baike.baidu.com/item/数据块)服务器上，XFS 可能因为这种 I/O 竞争无法获得最佳性能。另一个可能提高这个问题的严重性的因素是，日志写入被要求以同步方式提交 —— 它们必须被完全写入，之后对应实际数据的写入操作才能开始。

如果确实需要最佳的文件系统性能，XFS 提供了一个选项，允许把[日志](http://baike.baidu.com/item/日志)放置在一个分离的[物理设备](http://baike.baidu.com/item/物理设备)上。这只需要很小的物理空间。分离的设备有自己的 I/O 路径，如果该设备能对同步写入提供低延迟的路径，那么它将给整个文件系统的操作带来显著的性能提升。[SSD](http://baike.baidu.com/item/SSD)，或带有写回缓存的 RAID 系统是日志设备的合适候选，它们能满足这种性能要求。不过后者在遭遇断电时可能降低数据的安全性。要启用外部日志，只须以 logdev 选项挂载文件系统，并指定一个合适的日志设备即可。

**缺点**

  XFS 文件系统无法被收缩。

  历史上 XFS 上的[元数据](http://baike.baidu.com/item/元数据)操作曾比其它文件系统都慢，表现为在删除大量小文件时性能糟糕。该性能问题是被 [Red Hat](http://baike.baidu.com/item/Red Hat) 的 XFS 开发者 Dave Chinner 在代码中定位到的。使用一个叫 “延迟记录” 的挂载选项可以成数量级地提升[元数据](http://baike.baidu.com/item/元数据)操作的性能。该选项几乎把[日志](http://baike.baidu.com/item/日志)整个存在内存中。Linux 内核主线版本 2.6.35 中作为一个试验性特性引入了这个补丁，在 2.6.37 中使它成为了一个稳定的特性，并计划在 2.6.39 中把它作为默认的日志记录方法。早期测试显示在有少量线程的环境中其性能接近 EXT4，在大量线程的环境下超过了 EXT4 。

## Elf文件格式

ELF 代表 Executable and Linkable Forma，是一种对可执行文件、目标文件和库使用的文件格式，跟 Windows 下的 PE 文件格式类似。ELF 格式是是 UNIX 系统实验室作为 ABI（Application Binary Interface）而开发和发布的，早已经是 Linux 下的标准格式了。

ELF 主要包括三种类型文件：

- 可重定位文件 (relocatable)：编译器和汇编器产生的.o 文件，被 Linker 所处理
- 可执行文件 (executable)：Linker 对.o 文件进行处理输出的文件，进程映像
- 共享对象文件 (shared object)：动态库文件.so

ELF 文件从概念上来说包括了 5 个部分：

- ELF header，描述体系结构和操作系统等基本信息，指出 section header table 和 program header table 在文件的位置
- program header table，这个是从运行的角度来看 ELF 文件的，主要给出了各个 segment 的信息，在汇编和链接过程中没用
- section header table，这个保存了所有的 section 的信息，这是从编译和链接的角度来看 ELF 文件的
- sections，就是各个节区
- segments，就是在运行时的各个段

注意，经过上面解释我们可以看到，其实 sections 和 segments 占的一样的地方。这是从链接和加载的角度来讲的。左边是链接视图，右边是加载视图，sections 是程序员可见的，是给链接器使用的概念，而 segments 是程序员不可见的，是给加载器使用的概念。一般是一个 segment 包含多个 section。Windows 的 PE 就没有这个 program header table 和 section header table 点都统一为 section，只是在加载时会进行处理。所以 program header table 和 section header table 都是可选的。



## quota

磁盘配额工具



## 存储相关

**I/O，即 input/output**，输入指的是对存储系统写入数据，输出指的是从存储系统读出数据，I/O 简单的可以理解为磁盘的读写。 **目前常见的硬盘类型主要有 HDD 和 SDD**，HDD 主要的接口类型有 SATA 硬盘和 SAS 硬盘两种类型，SSD 则会有 SATA、M.2、PCIe 及 U.2 等不同接口类型，每一种磁盘的性能是不一样的。

**IOPS** 是很多企业最关注的存储性能指标，指的是单位时间内系统能处理的 I/O 请求数量，通常， **计算 IOPS 的基本公式是：（总的读 + 写的操作量）/ 时间（秒）。**

**IOPS 数值会受到很多因素的影响，**包括 I/O 负载特征 (读写比例，顺序和随机，工作线程数，队列深度，数据记录大小)、系统配置、操作系统、磁盘驱动等等。

例如，在企业业务的 IO 传输过程中，数据包会被分割成多块 (block)，交由存储阵列缓存或者磁盘处理，对于磁盘来说这样每个 block 在存储系统内部也被视为一个 I/O，存储系统内部由缓存到磁盘的的数据处理也会以 IOPS 来作为计量的指标之一，如果是小数据块顺序读写，就很可能因为缓存影响导致 IOPS 结果数值过高。

很多厂商的存储产品就会拿到最大的 IOPS 测试结果来宣传，结果导致很多厂商购买后由于业务场景不同，根本达不到宣传的 IOPS 性能。

因此在实际测试的时候，**除了常见的 4K 随机读 IOPS、4K 随机写 IOPS、64K 顺序读 IOPS、64K 顺序写 IOPS 指标测试，还会需要测试读写混合（例如 50% 读 50% 写、70% 读 30% 写）以及根据企业的业务场景类型来进行测试。**

延时又称时延、延时，处理单个 I/O 请求的速度。这一点非常重要，**因为能够处理 1000 IOPS 且平均延迟为 10ms 的存储子系统可能比能够处理 5000 IOPS 且平均延迟为 50ms 的子系统获得更好的应用程序性能。**特别是如果应用程序对延迟很敏感，比如数据库服务。

带宽是指平均每秒读写的数据量。在同样的延时指标下，增加并发可以提高吞吐量。

企业用户在选购存储的时候，延迟和带宽也是两个非常重要的指标，并不能单独的根据每个指标的大小来评判存储性能的好坏，**需要 IOPS、延迟、带宽三者结合来看。**
