---
title: 非关系型数据库
date: 2020-03-02 21:40:33
categories: IT
tags:
  - IT，Web,数据库
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OTjU.png
description: 非关系型数据库有关的知识，学习后端必备。
---

非关系型数据库有关的知识，学习后端必备。

<!--more-->

## NewSQL

### mvsqlite

分布式的sqlite

https://github.com/losfair/mvsqlite

### foundationdb

分布式的key-value数据库

https://github.com/apple/foundationdb

### vitess

云上的mysql数据库

#### PlanetScale





### TiDB



### CockroachDB

https://github.com/cockroachdb/cockroach

### VictoriaMetrics

VictoriaMetrics（简称 VM），是一个快速高效、经济并且可扩展的监控解决方案和时序[数据库](https://cloud.tencent.com/solution/database?from=10680)。VM 是一个新兴的监控解决方案，可以作为 [Prometheus](https://cloud.tencent.com/product/tmp?from=10680) 的长期远程存储方案，当然 VM 也可以完全取代 Prometheus，因为 VM 基本全兼容 Prometheus 配置文件、PromQL、各类API、数据格式等等。VM 架构简单，安装极其方便，可靠性高，在性能，成本，可扩展性方面表现出色，社区活跃，且与 Prometheus 生态绑定紧密。

简言之，VM 是一个完全兼容 Prometheus 协议且性能比 Prometheus 更好的、面向监控分析的、更方便使用的[时序数据库](https://cloud.tencent.com/product/ctsdb?from=10680)。

VM 提供单机版和集群版。如果对HA要求较低的话，我觉得单机版基本就能够满足中小型企业的监控需求了。如果您的每秒写入数据点数小于100万，官方也是默认推荐使用单机版，这个数量是个什么概念呢，如果只是做机器设备的监控，每个机器差不多采集200个指标，采集频率是10秒的话每台机器每秒采集20个指标左右，100万/20=5万台机器。

使用过后一句话推荐理由：极易配置与运维。

Prometheus 在大数据量和高并发查询下性能是有瓶颈的，为了解决这个问题，官方支持了20多种时序数据库作为其远端存储，最常用的比如：M3DB/Thanos/VM，一句话总结其优势：VM 配置运维更简单，Prometheus 协议兼容更好。

2.2 兼容性 + 增强的 MetricsQL

解决 Prometheus 高并发查询的瓶颈，因为兼容，所以直查 VM 与查询 Promethues 客端毫无差别，唯一的差别就是更快。

VM 可直接替换 Grafana 的 Prometheus 数据源，经测试，线上数据源替换后100%兼容。此外，除了 100% 兼容的PromQL，VM 还提供了增强的 MetricsQL。

2.3 低内存

更低的内存占用，官方对比 Prometheus，可以释放7倍左右内存空间（线上对比大概4倍）。

2.4 高压缩比

提供存储数据高压缩，官方说可以比 Prometheus 减少7倍的存储空间（线上对比大概是4~5倍）。

2.5 高性能

查询性能比 Prometheus 更快。

2.6 支持水平扩容&HA：

VM 集群版支持。

2.7 支持多租户

VM 集群版支持。

2.8 配置运维更简单

单机版安装下载解压后只需要一行命令：

```
nohup ./victoria-metrics -retentionPeriod=30d -storageDataPath=data &
```

近两年火热的夜莺监控，官网也默认推荐其作为后端存储库。没听过？一句话推荐语：比 Zabbix 更强！Open-Falcon 二代！Prometheus 企业版！



### M3 DB



### prometheus

prometheus监控系统分为四大部分

1.prometheus服务器：

prometheus server是prometheus组件中的核心部分，负责实现对监控数据的获取、存储以及查询

2.NodeExporter业务数据源：

业务数据通过Pull/Push两种方法推送数据到Prometheus Server

3.AlertManager报警管理器：

Prometheus通过设置报警规则，如果符合报警规则，那么就将报警推送到AlertManager，由其进行报警

4.可视化监控界面：

Prometheus收集到数据之后，由WebUI界面进行可视化数据展示。可以通过自定义的API客户端进行调用数据展示，也可以直接使用Grafana解决方案来展示



配置prometheus监控源

在prometheus.yml的scrape_configs节点下添加内容

```yaml
scrape_configs:
	- job_name: 'prometheus'
		static_configs:
			- targets: ['localhost:9090']
	
	- job_name: 'node'
	
```

其他配置信息

```yaml
--storage.tsdb.path: Prometheus写入数据库的位置，默认为data/
--storage.tsdb.retention.time: 何时删除旧数据，默认为15d
--storage.tsdb.retention.size: 要保留的最大存储块字节数。最旧的数据将最先被删除，默认为0或者禁用。
--storage.tsdb.wal-compression: 启用压缩预写日志
```



### Parca

安装

```shell
curl -sL https://github.com/parca-dev/parca/releases/download/v0.15.0/parca_0.15.0_`uname -s`_`uname -m`.tar.gz | tar xvfz - parca
```

docker

```shell
docker pull ghcr.io/parca-dev/parca:v0.15.0
```



### Loki

Loki 是 Grafana Labs 团队最新的开源项目，是一个水平可扩展，高可用性，多租户的日志聚合系统。它的设计非常经济高效且易于操作，因为它不会为日志内容编制索引，而是为每个日志流配置一组标签。项目受 Prometheus 启发，官方的介绍就是：`Like Prometheus, but for logs`，类似于 Prometheus 的日志系统

和其他日志系统不同的是，Loki 只会对你的日志元数据标签（就像 Prometheus 的标签一样）进行索引，而不会对原始的日志数据进行全文索引。然后日志数据本身会被压缩，并以 chunks（块）的形式存储在对象存储（比如 S3 或者 GCS）甚至本地文件系统。一个小的索引和高度压缩的 chunks 可以大大简化操作和降低 Loki 的使用成本。

Loki 支持多租户模式，租户之间的数据是完全分开的。多租户是通过一个租户 ID（用数字字母生成的字符串）实现的。当多租户模式被禁用后，所有请求都会在内部生成一个**`假的`**租户 ID

Loki 可以在本地小规模运行也可以横向扩展。Loki 自带单进程模式，可以在一个进程中运行所有需要的微服务。单进程模式非常适合于测试 Loki 或者小规模运行。对于横向扩展来说，Loki 的微服务是可以被分解成单独的进程的，使其能够独立扩展。

https://www.qikqiak.com/post/grafana-loki-usage/



### node-exporter

要采集目标的监控数据，首先要在被采集目标上安装采集组件，这种采集组件被称为exporter。prometheus官网有很多这种exporter，如RabbitMQ exporter、MySQL_server exporter、Memcached exporter等等。exporter会暴露一个http接口，prometheus通过http协议使用pull方式周期性拉取相应的数据

从https://prometheus.io/download/ 获取最新的Node exporter二进制下载包



### 删除历史数据

默认情况下，管理时间序列的api是被禁用的，要启用它，我们需要在prometheus的启动参数中添加--web.enable-admin-api这个参数

命令

删除某个标签匹配的数据

```shell
curl -X POST -g 'http://localhost:9090/api/v1/admin/tsdb/delete_series?match[]={instance=".*"}'
```

删除某个指标数据

```shell
curl -X POST -g 'http://localhost:9090/api/v1/admin/tsdb/delete_series?match[]={node_load1=".*"}'
```

根据时间删除

```shell
curl -X POST -g 'http://localhost:9090/api/v1/admin/tsdb/delete_series?match[]={node_load1=".*"}&start<2020-02-01T00:00:00Z&end=2020-02-04T00:00:00Z'
```



### 对比

https://zhuanlan.zhihu.com/p/571938375





### DynamoDB

Amazon DynamoDB 是一种全托管 NoSQL 数据库服务，提供快速而可预测的性能，能够实现无缝扩展。DynamoDB 可以免除操作和扩展分布式数据库的管理工作负担，因而无需担心硬件预置、设置和配置、复制、软件修补或集群扩展等问题。此外，DynamoDB 提供了加密静态，这可以消除在保护敏感数据时涉及的操作负担和复杂性。有关更多信息

DynamoDB 自动将表的数据和流量分布到足够数量的服务器上，以处理客户指定的请求容量和存储的数据量，同时保持一致且快速的性能。所有数据项目均存储在固态硬盘 (SSD) 中，并自动复制到某个 AWS 区域的多个可用区，以便提供数据自身的高可用性和数据持久性。可以使用全局表保持 DynamoDB 表在 AWS 区域间同步

https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/Introduction.html



### Cassandra

Apache Cassandra 是一个高度可扩展的高性能分布式数据库，旨在处理许多商用服务器上的大量数据，提供高可用性而没有单点故障。它是 NoSQL 数据库的一种。

除了 Cassandra，我们还有以下非常流行的 NoSQL 数据库 -

- **Apache HBase**-HBase 是一种以 Google 的 BigTable 建模的开源，非关系，分布式数据库，并使用 Java 编写。它是 Apache Hadoop 项目的一部分，在 HDFS 之上运行，为 Hadoop 提供类似 BigTable 的功能。
- **MongoDB**-MongoDB 是一个跨平台的面向文档的数据库系统，它避免使用传统的基于表的关系数据库结构，而是使用具有动态模式的类似 JSON 的文档，从而使数据在某些类型的应用程序中的集成更加容易和快捷。

Apache Cassandra 是一个开源，分布式和分散 / 分布式存储系统（数据库），用于管理分布在世界各地的大量结构化数据。它提供高可用性服务，没有单点故障。

以下列出了 Apache Cassandra 的一些值得注意的地方 -

- 它具有可伸缩性，容错性和一致性。
- 它是一个面向列的数据库。
- 其分发设计基于亚马逊的 Dynamo 及其在 Google 的 Bigtable 上的数据模型。
- 它创建于 Facebook，与关系数据库管理系统截然不同。
- Cassandra 实现了 Dynamo 风格的复制模型，没有单点故障，但是添加了更强大的 “column family” 数据模型。
- 一些大型公司（例如 Facebook，Twitter，Cisco，Rackspace，ebay，Twitter，Netflix 等）正在使用 Cassandra。

Cassandra 由于其出色的技术特性而变得如此受欢迎。以下是 Cassandra 的一些功能：

- 弹性可扩展性– Cassandra 具有高度可扩展性；它允许添加更多硬件，以根据需求容纳更多客户和更多数据。
- 始终在线 - Cassandra 没有单点故障，并且可以连续用于无法承受故障的关键业务应用程序。
- 快速的线性规模性能 - Cassandra 具有线性可扩展性，即，随着集群中节点数量的增加，它可以提高吞吐量。因此，它保持了快速的响应时间。
- 灵活的数据存储 - Cassandra 可容纳所有可能的数据格式，包括：结构化，半结构化和非结构化。它可以根据需要动态适应对数据结构的更改。
- 轻松进行数据分发 - Cassandra 通过在多个数据中心之间复制数据，提供了在所需位置分发数据的灵活性。
- 事务支持 - Cassandra 支持原子性，一致性，隔离性和持久性（ACID）等属性。
- 快速写入 - Cassandra 旨在在廉价的商品硬件上运行。它执行快速的写入，并且可以存储数百 TB 的数据，而不会牺牲读取效率。

Cassandra 的设计目标是在多个节点上处理大数据工作负载而不会出现任何单点故障。 Cassandra 在其节点之间具有对等分布式系统，并且数据分布在集群中的所有节点之间。

- 集群中的所有节点都扮演相同的角色。每个节点都是独立的，并同时互连到其他节点。
- 集群中的每个节点都可以接受读写请求，而不管数据实际位于集群中的何处。
- 当某个节点发生故障时，可以从网络中的其他节点处理读 / 写请求。

数据复制

在 Cassandra 中，集群中的一个或多个节点充当给定数据段的副本。如果检测到某些节点响应的值过时，则 Cassandra 会将最新值返回给客户端。返回最新值后，Cassandra 在后台执行读取修复以更新过时的值

组成部分如下 -

- **Node** − 它是存储数据的地方。
- **Data center** − 它是相关节点的集合。
- **Cluster** − 是包含一个或多个数据中心的组件。
- **Commit log** − 是 Cassandra 中的崩溃恢复机制。每个写操作都会写入 commit log。
- **Mem-table** − 内存表是驻留内存的数据结构。commit log 后，数据将被写入内存表。有时，对于 single-column family，将有多个内存表。
- **SSTable** − 它是一个磁盘文件，当其内容达到阈值时，会将数据从内存表中刷新到该磁盘文件中。
- **Bloom filter** − 这些仅是用于测试元素是否为集合成员的快速，不确定性算法。这是一种特殊的缓存。每次查询后都会访问 Bloom 筛选器。

用户可以使用 Cassandra 查询语言（CQL）通过其节点访问 Cassandra。 CQL 将数据库 Keyspace 视为表。程序员使用 cqlsh 进行查询。

客户端接近任何节点以进行读写操作。该节点（协调器）在客户端和保存数据的节点之间扮演代理角色。

`写操作`：节点的每个写入活动均由写入节点中的 **commit logs** 捕获。之后，将捕获数据并将其存储在 **mem-table** 中。每当 **mem-table** 已满时，数据将被写入 **SStable** 数据文件中。所有写入将自动分区并在整个集群中复制。 Cassandra 定期合并 SSTable，丢弃不必要的数据。

`读操作`: 在读取操作期间，Cassandra 从 mem-table 中获取值，并检查 Bloom 筛选器以找到保存所需数据的适当 SSTable。

## 向量数据库

https://github.com/milvus-io/milvus

在pg数据库中插入向量

https://github.com/pgvector/
