---
title: SRE相关
date: 2020-03-06 21:40:33
categories: IT
tags:
    - IT,Web，IOS
toc: true
thumbnail: 
---

​	SRE概念/技术栈

<!--more-->

## 一致性组

一致性组是指有业务关联的远程复制的集合。一致性组中的所有远程复制可以同时进行分裂、同步、主从切换等操作。

在大中型数据库应用中，数据、日志、修改信息等存储在存储系统的不同LUN中，缺少其中一个LUN的数据，都将导致其他LUN中的数据失效，无法继续使用。如果需要同时对这些LUN进行远程容灾，那么就要考虑如何保持多个远程复制中的数据在时间上的一致性。此时，通过远程复制提供一致性组功能就可以保证多个LUN之间镜像数据时间一致性。

一致性复制组能够实现业务系统跨多块云盘的容灾场景下，对多块云盘的异步复制做统一管理和操作，同时可以确保同一复制组内的多块云盘数据能够恢复到同一个时间点，以便实现容灾场景下实例级别或者多实例级别的容灾保护。

## 可观测性

可观测性三本柱:  **Logging**、**Metrics** 及 **Distributed Tracing**

 **Distributed Tracing** 分布式追踪建立在微服务的前提下

Distributed Tracing 簡單的來說就是在監控 request 在 distributed system 或是 microservices 中的行為的技術

Distributed Tracing 就是為了解決跨服務的日誌記錄而誕生的。

Trace (追蹤) 用來描述網路服務在回應一個 request 時觸發的一系列事件，這些事件可能跨越了不同 process 、network boundary 或是 subsystem boundary。

為了記錄事件在跨服務間的從屬關係以及發生順序， Distributed Tracing 規範了一系列的 headers (以 HTTP 為主，但也有 Message Queue 與 RPC 的實作)，每個子服務必須要乖乖收集這些 headers，並在向子服務送 request 時一路 propagate ( 傳遞 ) 給所有子服務。

**Trace (追蹤) 與 Span (跨度)** 是 Distributed Tracing 裡最重要的兩個元件。每個 Span 還會帶有一個 **SpanContext** 來描述多個 Span 之間共有的狀態。

**Trace**

Trace 用來描述一個 request，由一到多個 Span 組成。

**Span**

Span 為在各服務中發生的多個不同階段的資訊，一個 Span 包含：

- 事件名稱
- 開始與結束時間
- Attributes (屬性)：一系列的 key-value
- 零到多個事件 (event)：每個事件有 Name, Timestamp 及多個 Attributes
- Parent Span ID：連結到零或多個 Span
- SpanContext ID

**SpanContext**

SpanContext 中包含 Span ID，也負責傳遞由 parent span 設定的 options。

- **Trace ID**：Trace 的 ID，隨機產生的 16 bytes 值，用來 group 跨越服務邊界的所有屬於同個 Trace 的 Spans。
- **Span ID**：Span 的 ID，隨機產生的 8 bytes 值，用來識別 Span。當 SpanID 被傳遞至下一層的子 Span 時，SpanID 會成為子 Span 中的 Parent Span ID。
- **TraceFlags**：Trace 的 options。長度為 1-byte 值 (含 8 bit)，目前的規範中只定義了一個 bit，為 Sampling Bit (0x1)，代表此 Trace 是否有被 sample (取樣) 到。
- **TraceState**：TraceState 讓 vendor 能夾帶自行定義的 key-value pairs，能用來彈性的傳遞額外的資訊或是處理 legacy field。



https://lockercho.medium.com/observability-%E4%B8%89%E6%9C%AC%E6%9F%B1%E4%B9%8B-distributed-tracing-%E4%BB%8B%E7%B4%B9-dc4e0a50cf97

## prometheus

https://songjiayang.gitbooks.io/prometheus/content/promql/summary.html

### PromQL



## loki



### logql

https://www.qikqiak.com/k8strain2/logging/loki/logql/#:~:text=%E5%8F%97PromQL%20%E7%9A%84%E5%90%AF%E5%8F%91%EF%BC%8CLoki,%E6%9F%A5%E8%AF%A2%E8%BF%94%E5%9B%9E%E6%97%A5%E5%BF%97%E8%A1%8C%E5%86%85%E5%AE%B9

受 PromQL 的启发，Loki 也有自己的查询语言，称为 LogQL，它就像一个分布式的 grep，可以聚合查看日志。和 PromQL 一样，LogQL 也是使用标签和运算符进行过滤的，主要有两种类型的查询功能：

- 查询返回日志行内容
- 通过过滤规则在日志流中计算相关的度量指标

一个基本的日志查询由两部分组成。

- `log stream selector`（日志流选择器）
- `log pipeline`（日志管道）

由于 Loki 的设计，所有 LogQL 查询必须包含一个日志流选择器。

**日志流选择器**决定了有多少日志流（日志内容的唯一来源，如文件）将被搜索到，一个更细粒度的日志流选择器将搜索到流的数量减少到一个可管理的数量。所以传递给日志流选择器的标签将影响查询执行的性能。

而日志流选择器后面的**日志管道**是可选的，日志管道是一组阶段表达式，它们被串联在一起应用于所过滤的日志流，每个表达式都可以过滤、解析和改变日志行内容以及各自的标签。

## M3

prometheus的go版本

https://github.com/m3db/m3



## zabbix

https://www.zabbix.com/features

[Zabbix](https://www.zabbix.com/cn) 是一个开源的企业级监控解决方案，支持实时监控数千台服务器，虚拟机和网络设备，采集百万级监控指标。支持分布式架构，通过统一的 Web 界面监控自动监控大型动态环境

https://support.websoft9.com/docs/gogs/solution

## vector

https://github.com/vectordotdev/vector

安装

```shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.vector.dev | bash
```



## datadog-agent

监控指标

https://github.com/DataDog/datadog-agent



## VictoriaMetrics

https://github.com/VictoriaMetrics/VictoriaMetrics

https://cloud.tencent.com/developer/article/1945323

VictoriaMetrics 监控组件（以下简称 VM）号称比 [Prometheus](https://cloud.tencent.com/product/tmp?from_column=20065&from=20065) 快了至少 3 倍，内存占用比 Prometheus 小了 7 倍

CPU的并发

**区分 IO 协程和计算协程**

see:  VictoriaMetrics-1.72.0-cluster/app/vmstorage/transport/server.go#L261

IO 协程执行收包、解包的任务，然后就把请求数据扔到队列，不再做复杂的其他计算。

计算协程从 channel 中获取任务来执行计算。

**计算协程数量与 CPU 核数相当，且提供了协程优先级的处理策略**

计算协程分为两种，写入协程和查询协程。写入协程与 CPU 核数相当，查询协程是 CPU 核数的两倍。查询协程更多的原因是查询期间可能引发 IO，所以需要并发数多于核数。

在 vm-storage 的场景里，写入的优先级高于查询。

在写入协程中，设置了一个长度为 CPU 核数的队列 (channel)，每次准备写入前先入队，如果队满就等待。这样就严格限制了写入协程的并发。同时，队列如果满，证明高优先级的写入协程没有被调度。

vm-storage 提供了机制让查询协程主动让出，在写入队列满的时候通知查询协程，避免查询太多影响写入。

https://zhuanlan.zhihu.com/p/691153309

## Upptime

https://github.com/upptime/upptime



## kuma

监控工具

https://github.com/louislam/uptime-kuma

https://cloud.tencent.com/developer/article/1917098



## kener

https://kener.ing/



## nightingale

企业级的监控平台

https://github.com/ccfos/nightingale



## falcon-plus

https://github.com/open-falcon/falcon-plus/tree/master



## openobserve

https://github.com/openobserve/openobserve

轻量级的监控工具



## opencost

跨云监控k8s集群

https://github.com/opencost/opencost



## 哪吒监控

https://nezha.wiki/



## nginx-module-vts

https://github.com/vozlt/nginx-module-vts#version

Nginx 虚拟主机流量状态模块

提供对虚拟主机状态信息的访问。它包含当前状态，例如服务器、上游、缓存。这类似于 nginx plus 的实时活动监控。内置的 html 也取自旧版本的演示页面。

安装

```shell
wget https://github.com/vozlt/nginx-module-vts/archive/refs/tags/v0.2.2.zip

 unzip nginx-module-vts_v0.2.2.zip
```

编辑配置文件

```conf
http {
    #---隐藏其他配置
    vhost_traffic_status_zone;
    vhost_traffic_status_filter_by_host on;
    server {
        #---隐藏其他的路由
        location /status {
            vhost_traffic_status_display;
            vhost_traffic_status_display_format html;
        }     
    }
}
```

当我们服务配置完成后，可以访问下面的地址来获取 Prometheus 数据

```shell
https://localhost/status/format/prometheus
```



## 分布式追踪

### jaeger

https://github.com/jaegertracing/jaeger?tab=readme-ov-file



### zipkin

https://github.com/openzipkin/zipkin
