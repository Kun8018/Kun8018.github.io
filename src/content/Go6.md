---
title: Golang语言开发(五)
date: 2020-03-02 21:40:33
categories: 技术博客
tags:
    - Web,IT,Go
toc: true
thumbnail: https://s1.ax1x.com/2020/04/20/J1Iu4O.th.jpg
---

　　本篇主要内容为分布式系统

<!--more-->

## 分布式系统

### 延时任务系统

我们在做系统时，很多时候是处理实时的任务，请求来了马上就处理，然后立刻给用户以反馈。但有时也会遇到非实时的任务，比如确定的时间点发布重要公告。或者需要在用户做了一件事情的X分钟/Y小时后，对其特定动作，比如通知、发券等等。

如果业务规模比较小，有时我们也可以通过数据库配合轮询来对这种任务进行简单处理，但上了规模的公司，自然会寻找更为普适的解决方案来解决这一类问题。

一般有两种思路来解决这个问题：

1. 实现一套类似crontab的分布式定时任务管理系统。
2. 实现一个支持定时发送消息的消息队列。

两种思路进而衍生出了一些不同的系统，但其本质是差不多的。都是需要实现一个定时器（timer）。在单机的场景下定时器其实并不少见，例如我们在和网络库打交道的时候经常会调用`SetReadDeadline()`函数，就是在本地创建了一个定时器，在到达指定的时间后，我们会收到定时器的通知，告诉我们时间已到。这时候如果读取还没有完成的话，就可以认为发生了网络问题，从而中断读取。

定时器的实现在工业界已经是有解的问题了。常见的就是时间堆和时间轮。

最常见的时间堆一般用小顶堆实现，小顶堆其实就是一种特殊的二叉树，

小顶堆的好处是什么呢？对于定时器来说，如果堆顶元素比当前的时间还要大，那么说明堆内所有元素都比当前时间大。进而说明这个时刻我们还没有必要对时间堆进行任何处理。定时检查的时间复杂度是`O(1)`。

当我们发现堆顶的元素小于当前时间时，那么说明可能已经有一批事件已经开始过期了，这时进行正常的弹出和堆调整操作就好。每一次堆调整的时间复杂度都是`O(LgN)`。

Go自身的内置定时器就是用时间堆来实现的，不过并没有使用二叉堆，而是使用了扁平一些的四叉堆。

小顶堆的性质，父节点比其4个子节点都小，子节点之间没有特别的大小关系要求。

四叉堆中元素超时和堆调整与二叉堆没有什么本质区别。

用时间轮来实现定时器时，我们需要定义每一个格子的“刻度”，可以将时间轮想像成一个时钟，中心有秒针顺时针转动。每次转动到一个刻度时，我们就需要去查看该刻度挂载的任务列表是否有已经到期的任务。

从结构上来讲，时间轮和哈希表很相似，如果我们把哈希算法定义为：触发时间%时间轮元素大小。那么这就是一个简单的哈希表。在哈希冲突时，采用链表挂载哈希冲突的定时器。

除了这种单层时间轮，业界也有一些时间轮采用多层实现，这里就不再赘述了。

每一个实例每隔一小时，会去数据库里把下一个小时需要处理的定时任务捞出来，捞取的时候只要取那些`task_id % shard_count = shard_id`的那些任务即可。

当这些定时任务被触发之后需要通知用户侧，有两种思路：

1. 将任务被触发的信息封装为一条消息，发往消息队列，由用户侧对消息队列进行监听。
2. 对用户预先配置的回调函数进行调用。



### 负载均衡

如果我们不考虑均衡的话，现在有n个服务节点，我们完成业务流程只需要从这n个中挑出其中的一个。有几种思路:

1. 按顺序挑: 例如上次选了第一台，那么这次就选第二台，下次第三台，如果已经到了最后一台，那么下一次从第一台开始。这种情况下我们可以把服务节点信息都存储在数组中，每次请求完成下游之后，将一个索引后移即可。在移到尽头时再移回数组开头处。
2. 随机挑一个: 每次都随机挑，真随机伪随机均可。假设选择第 x 台机器，那么x可描述为`rand.Intn()%n`。
3. 根据某种权重，对下游节点进行排序，选择权重最大/小的那一个。

当然了，实际场景我们不可能无脑轮询或者无脑随机，如果对下游请求失败了，我们还需要某种机制来进行重试，如果纯粹的随机算法，存在一定的可能性使你在下一次仍然随机到这次的问题节点。

洗牌算法

考虑到我们需要随机选取每次发送请求的节点，同时在遇到下游返回错误时换其它节点重试。所以我们设计一个大小和节点数组大小一致的索引数组，每次来新的请求，我们对索引数组做洗牌，然后取第一个元素作为选中的服务节点，如果请求失败，那么选择下一个节点重试，以此类推

洗牌算法 有两个隐藏的隐患:

1. 没有随机种子。在没有随机种子的情况下，`rand.Intn()`返回的伪随机数序列是固定的。
2. 洗牌不均匀，会导致整个数组第一个节点有大概率被选中，并且多个节点的负载分布不均衡。



### 分布式搜索引擎

数据库系统本身要保证实时和强一致性，所以其功能设计上都是为了满足这种一致性需求。比如write ahead log的设计，基于B+树实现的索引和数据组织，以及基于MVCC实现的事务等等。

关系型数据库一般被用于实现OLTP系统，所谓OLTP，援引wikipedia：

在线交易处理（OLTP, Online transaction processing）是指透过信息系统、电脑网络及数据库，以线上交易的方式处理一般即时性的作业数据，和更早期传统数据库系统大量批量的作业方式并不相同。OLTP通常被运用于自动化的数据处理工作，如订单输入、金融业务…等反复性的日常性交易活动。和其相对的是属于决策分析层次的联机分析处理（OLAP）。

在互联网的业务场景中，也有一些实时性要求不高(可以接受多秒的延迟)，但是查询复杂性却很高的场景。举个例子，在电商的WMS系统中，或者在大多数业务场景丰富的CRM或者客服系统中，可能需要提供几十个字段的随意组合查询功能。这种系统的数据维度天生众多，比如一个电商的WMS中对一件货物的描述，可能有下面这些字段：

> 仓库id，入库时间，库位分区id，储存货架id，入库操作员id，出库操作员id，库存数量，过期时间，SKU类型，产品品牌，产品分类，内件数量

除了上述信息，如果商品在仓库内有流转。可能还有有关联的流程 id，当前的流转状态等等。

想像一下，如果我们所经营的是一个大型电商，每天有千万级别的订单，那么在这个数据库中查询和建立合适的索引都是一件非常难的事情。

在CRM或客服类系统中，常常有根据关键字进行搜索的需求，大型互联网公司每天会接收数以万计的用户投诉。而考虑到事件溯源，用户的投诉至少要存2~3年。又是千万级甚至上亿的数据。根据关键字进行一次like查询，可能整个MySQL就直接挂掉了。

这时候我们就需要搜索引擎来救场了。



### 分布式配置管理

在分布式系统中，常困扰我们的还有上线问题。虽然目前有一些优雅重启方案，但实际应用中可能受限于我们系统内部的运行情况而没有办法做到真正的“优雅”。比如我们为了对去下游的流量进行限制，在内存中堆积一些数据，并对堆积设定时间或总量的阈值。在任意阈值达到之后将数据统一发送给下游，以避免频繁的请求超出下游的承载能力而将下游打垮。这种情况下重启要做到优雅就比较难了。

所以我们的目标还是尽量避免采用或者绕过上线的方式，对线上程序做一些修改。比较典型的修改内容就是程序的配置项。

我们使用etcd实现一个简单的配置读取和动态更新流程，以此来了解线上的配置更新流程。

配置定义

```json
etcdctl get /configs/remote_config.json
{
    "addr" : "127.0.0.1:1080",
    "aes_key" : "01B345B7A9ABC00F0123456789ABCDAF",
    "https" : false,
    "secret" : "",
    "private_key_path" : "",
    "cert_file_path" : ""
}
```

新建etcd client

```go
cfg := client.Config{
    Endpoints:               []string{"http://127.0.0.1:2379"},
    Transport:               client.DefaultTransport,
    HeaderTimeoutPerRequest: time.Second,
}
```

获取配置

```go
resp, err = kapi.Get(context.Background(), "/path/to/your/config", nil)
if err != nil {
    log.Fatal(err)
} else {
    log.Printf("Get is done. Metadata is %q\n", resp)
    log.Printf("%q key has %q value\n", resp.Node.Key, resp.Node.Value)
}
```

配置膨胀

随着业务的发展，配置系统本身所承载的压力可能也会越来越大，配置文件可能成千上万。客户端同样上万，将配置内容存储在etcd内部便不再合适了。随着配置文件数量的膨胀，除了存储系统本身的吞吐量问题，还有配置信息的管理问题。我们需要对相应的配置进行权限管理，需要根据业务量进行配置存储的集群划分。如果客户端太多，导致了配置存储系统无法承受瞬时大量的QPS，那可能还需要在客户端侧进行缓存优化，等等。

这也就是为什么大公司都会针对自己的业务额外开发一套复杂配置系统的原因。

配置版本管理

在配置管理过程中，难免出现用户误操作的情况，例如在更新配置时，输入了无法解析的配置。这种情况下我们可以通过配置校验来解决。

有时错误的配置可能不是格式上有问题，而是在逻辑上有问题。比如我们写SQL时少select了一个字段，更新配置时，不小心丢掉了json字符串中的一个field而导致程序无法理解新的配置而进入诡异的逻辑。为了快速止损，最快且最有效的办法就是进行版本管理，并支持按版本回滚。

在配置进行更新时，我们要为每份配置的新内容赋予一个版本号，并将修改前的内容和版本号记录下来，当发现新配置出问题时，能够及时地回滚回来。

常见的做法是，使用MySQL来存储配置文件或配置字符串的不同版本内容，在需要回滚时，只要进行简单的查询即可。

### 分布式异步工作流

在实际生产中存在很多这样的业务场景：一个完整业务流程由许多单体业务步骤组成，每个单体业务可以分布式的异步完成，但某些业务步骤之间又存在一定的顺序依赖关系。

这样的场景下，整个业务便呈现一种流式的排布。如果没有顺序依赖的话，使用简单的多生产者多消费者模型即可满足我们的需求，但当需要对顺序进行控制或依赖前一个过程的结果时，工作流便成了一个非常方便的解决方案。

举例来说，设想一个视频站点的业务。用户上传视频后，我们需要将视频送去给图像组做水印检测、送去给推荐组做视频分类，之后我们需要对图像组修改过的视频进行格式调整，再进行多种编码格式、码率、分辨率的视频调整，最终将每个步骤产生的视频汇总，更新这个视频在数据库中存储的相关资源链接。在这个过程中转码操作依赖于图像组给回的视频源，最终视频更新资源要使用各个阶段输出的资源链接，这是明显的顺序和结果依赖；而多种编码、码率、分辨率的调整完全可以并发的异步进行，可以视为是无状态的任务。这种情景下既要保证依赖可靠，又要提升性能和处理速度，**分布式的异步工作流**便成为了首选。

#### 任务调度框架

基于 Golang 的异步任务工作流框架 Machinery与Temporal（[Temporal](https://github.com/temporalio/temporal) 前身是 Uber 的内部工作流组件 [Cadence](https://github.com/uber/cadence)，是一个封装的比较好的工作流编排引擎）

Machinery 本质上依赖于单个中间件做队列，Worker 做全异步的消费，即多生产者多消费者模型。

Temporal（Cadence）是一个引擎，本身可视为一个中间件，其依赖外部的相关组件做持久化、通信等，由自身来实现工作流编排和调度，实现任务的收集与分发。Temporal 本质上有多个组件组成，但如果使用 Docker 的话，对客户端来说也是单点接入的，实际使用起来并不会增加复杂度

代码架构

两者的代码结构类似，从微服务的角度来看，都是在 Gateway 层做触发，即生产者；在 Service 层做 Worker，即消费者

不同之处在于 Machinery 偏向于在生产侧做简单工作流编排，而 Temporal 偏向于在 Worker 消费侧做复杂工作流编排。

| ** Machinery** | **Temporal**（**Cadence**）                                  |                                                              |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 介绍           | 开源异步队列任务框架，支持工作流编排，Github 4400 stars      | Uber 开发的异步工作流引擎，Github 800+(4100) stars           |
| 任务队列       | RabbitMQ、Redis、MongoDB等中间件                             | 自身维护消息队列                                             |
| 持久层         | 无                                                           | Cassandra、MySQL 等                                          |
| 历史浏览       | 无                                                           | Web页面可视化、ES 搜索                                       |
| 架构部署       | 简单，本身是一个框架，无新引入组件                           | 复杂，要新引入部署一个服务并引入服务发现，不使用 Docker 时二进制部署较困难 |
| 代码结构       | 简陋，使用 JSON 数据通信，代码中字符串定义会较多，较易读；多任务工作流编排倾向于在 Trigger 侧 | 优雅，使用信号传递通信，代码集成度高，不易读；多任务工作流编排倾向于在 Worker 侧 |
| 功能性         | 请求单位为一个 task，需要在 Trigger 侧手动处理各种结果和分支功能性简单 | 请求单位为一个 workflow，任务调度与结果处理在 Worker 侧的 workflow 中已经编排，功能性强 |
| 实现           | 多生产者多消费者框架，中间件作为队列，Trigger 编排并生产请求，Worker 异步消费请求 | Temporal 服务作为编排调度引擎，Worker 先编排工作流注册到服务，Trigger 直接对服务发送调度请求 |

简而言之，Temporal 是原 Cadence 联合创始人离开 Cadence 后基于其 Fork的新分支，从某种程度上来讲，Temporal 可能是 Cadence 的一个更商业化的版本，毕竟后者原先只是 Uber 内部的一个组件。也正如这个问答中有人提到的，Temporal 作为新公司产品开发后，可能会更多的拥抱业界普遍性的业务场景，相比较起来，它们也正处在高速的版本迭代中。

但 Temporal 和 Cadence 到底该使用哪一个呢？这恐怕需要大家自己来定夺，我简单罗列一下两者目前的区别：

- Temporal 的特点
  1. 将数据通信、存储由 Cadence 原来使用的 Thrift 更改为了[Protocol Buffers](https://developers.google.com/protocol-buffers)，当然这可能对于使用者来说感觉不明显
  2. 组件间通信由原 Uber 的TCP 多路复用协议 [TChannel](https://github.com/uber/tchannel) 更改为了 [gRPC](https://grpc.io/)，gRPC 的好处是客户端可以轻松使用其自带的 [DNS 解析](https://github.com/grpc/grpc/blob/master/doc/naming.md) 实现负载均衡
  3. 组件间通信全面支持 TLS 加密，双向 TLS 加密对于安全性要求较高的场景更适用
  4. 优化了组件间配置及工作流数据流转
  5. 优化了客户端使用以及多语言的支持
  6. 高速的版本迭代（你很难说这是个优点还是个缺点，毕竟带来新 feature 的同时也可能带来新 bug）
- Cadence 的特点
  1. Cadence 的 Web 服务可以对多集群的数个 Server 同时连接，从而获取全部的历史。但 Temporal 当前只能连接一个 Server，这取决于通信方式采用 gRPC 的局限性，当然官方也表示该功能正在积极开发中
  2. Cadence 声称其已经完全移除了 Kafka 的依赖，而 Temporal 的多集群化仍需要 Kafka 的支持
  3. Cadence 正在推广自己的开源社区化，会有更多的人加入到 Cadence 开发中，目前来看其迭代速度并不亚于 Temporal

#### Machinery

异步通信框架

```go
import (
  "github.com/RichardKnop/machinery/v2"
  backendsiface "github.com/RichardKnop/machinery/v2/backends/iface"
  brokersiface "github.com/RichardKnop/machinery/v2/brokers/iface"
  locksiface "github.com/RichardKnop/machinery/v2/locks/iface"
)

var broker brokersiface.Broker
var backend backendsiface.Backend
var lock locksiface.Lock
server := machinery.NewServer(cnf, broker, backend, lock)
// server.NewWorker("machinery", 10)
```

github

```shell
go get github.com/RichardKnop/machinery/v2
```



#### Temporal

Temporal是一种分布式、可扩展、持久且高度可用的编排引擎，用于以可扩展和弹性的方式执行一步长时间运行的业务逻辑。

Temporal由四部分组成Start、Temporal Server、Worker、Bank

- Start：工作流的创建者/发起者。可以将不同的Activity(也就是Worker实现的具体的执行逻辑模块，每一个Activity都有一个名字)组织成一个Workflow，并且开启workflow。
- Temporal Server：存储所有工作流的数据、状态的中间件，整个工作依赖于该server（后续简写为TS）
- Worker：实际进行逻辑处理的执行者。该模块实现具体的执行逻辑，并且在启动的时候注册到TS。
- Bank：官方给的示例，可以理解为DB，TS的存储模块。

具体的流程描述： 

1.启动Temporal Server。 

2.启动Temporal Client，也就是Worker，监听TS，循环获取待执行的工作流。 

3.Start创建一个工作流，封装参数，调用sdk的api(rpc)发送到TS。Worker拉取到工作流开始逻辑处理

woker是Activity和Workflow的包装，worker的唯一工作就是执行Activity和Workflow并将结果返回给TS。 一个

Wokeflow包含多个Activity，对Activity进行编排，多个Activity可以并行，也可以同步（阻塞到都某个Activity执

行完毕）。其底层会阻塞到Future.Get()方法上。

在Temporal文档中，对Workflow的描述分为了Workflow Type、Workflow Definition和Workflow Execution。我们先来了解一下这些概念的定义，才能理解它的使用

Workflow Type

Workflow的类型，其实就是一个Workflow的name，因为Temporal是通过name来区分工作流的，所以工作流的name就代表了类型，同时同一Worker内不能有重名的Workflow。

既然是一种类型，就可以执行多次，那么每次执行，就会实例化为一个Workflow Execution。

同时这些Workflow的name，是Worker的队列之间隔离的，也就是说如果你有多个Worker，都有名叫Workflow-1的工作流，但是具体实现不同，那么在不同的Worker里会映射到不同的工作流定义，也就是Workflow Definition。

Workflow Definition

工作流定义，这个很好理解，就是你描述具体Workflow执行的代码，也就是开发人员来定义的部分。

目前Temporal支持的SDK有Go、Java、PHP和TypeScript。

虽然是开发人员来定义的流程，但是Temporal对于工作流定义的代码还有一些限制：

1.代码必须具备确定性

比如我们在代码里存在分支判断，但是分支判断的条件，是不确定的，也就是随着你执行的次数或者时间到推移，每次调用这段代码可能得到的结果都不是确定的，那么就代表这段代码不具备确定性，这种代码写在Workflow里是不允许的。

针对workflow而言，如果一个Workflow Execution已经发起之后，就不能再修改Workflow Definition的代码了。比如我们开启了一个定时的Workflow，但是在定时器到达之前，重新修改并提交了Workflow Definition的代码，那么到定时器触发的时候，工作流将执行失败。

那么怎么避免这种不确定性呢？

Temporal提供的方案是对Workflow进行版本化管理，版本化我们后面再说。还有就是Temporal API提供了一些对于非确定性的替代方案，比如提供了等待、随机数等方法。

2.尽量处理可能发生的异常

开发人员不需要关心工作流在执行过程中由于Worker进程或者Temporal集群发生故障而导致的中断，因为这样的中断Temporal是可以恢复的。

但是需要处理代码中可能出现的异常，因为这是唯一可能导致工作流失败的原因。

Workflow Execution

工作流执行，表示一种工作流类型对应的一种工作流定义的一次执行流程。

每个Workflow Execution都有自己的本地状态，并且拥有本地状态的独占访问权。所有的Workflow Execution之间并行执行，并且通过Signals互相通信，也可以利用Activities和外部环境通信。

Workflow Execution的执行历史事件最大限制为50000个，数据存储最大限制为50MB。每产生10000个事件会产生一个警告，达到50000个事件或者数据达到50MB时，工作流将会被强制停止

虽然单次Workflow Execution对于大小和吞吐量是有一定限制的，但是Temporal允许数百万到数十亿个工作流通知执行。

Workflow Execution具有以下特点：

持久性：一次Workflow Execution就是我们对Workflow Definition的执行，无论这个过程会持续几秒还是几年，这就是所谓的持久性。

可靠性：可靠性代表在Workflow Execution执行过程中对故障的响应能力。Temporal对于由于技术设施中断导致的故障具有很好的恢复性，可以保持Workflow Execution在中断时的状态，以及从最新的状态恢复执行。

可扩展性：可扩展性是指Temporal的负载能力。Temporal集群和Worker进程的设计和性质，让Temporal可以支撑数十亿个工作流同时执行。

支持异步调用：Workflow Execution就是再重复向Temporal平台发送指令和等待指令返回的过程。

在进行下面这些动作时，支持异步阻塞等待：

1. 开启一个子工作流时，可以在子工作流开启后阻塞，知道子工作流执行结果返回；
2. 发送Signal时，可以在发送Signal之后阻塞，等待Signal返回；
3. 开始Activity执行时，可以在开启Activity后阻塞，直到Activity执行结果返回；
4. 向一个Workflow Execution发送取消请求时，可以异步等待请求结果；
5. 开启定时器时，可以阻塞流程，直到定时器触发；
6. 通过调用await方法主动阻塞流程。

有状态的：

Workflow Execution的状态分为Open和Closed两种

只有Running状态属于Open，无论是正在处理流程还是在阻塞等待，状态都体现为Running。

Closed包含六种状态，表示工作流已经结束，六种状态分别为：

1. Completed：Workflow Execution已经成功结束；
2. Continued-As-New：定时Workflow到达定时周期时，开启新的Workflow，之前的Workflow会变成此状态；
3. Terminated：Workflow Execution被终止；
4. Cancelled：Workflow Execution被取消，意味着成功处理了一个取消请求；
5. Failed：Workflow Execution发生异常并执行失败；
6. Timed Out：Workflow Execution触发了超时限制；

Workflow Id

Workflow Id是Workflow Execution的流程标识符，是可以自定义的具有业务语义的一串字符串，例如客户id或者订单id等。

在一个namespace内，对于同一个Workflow Id，最多只有一个Workflow Execution处于Open状态，也就是Running。如果尝试开启一个与正在Running的Workflow 具有相同Id的Workflow，会提示“Workflow execution already started”。

一个Workflow Execution可以通过Namespace+Workflow Id+Run Id来唯一标识。

Workdflow Id重用策略

通过配置Workflow Id Reuse Policy，可以指定开启新Workflow Execution时可不可以使用已有的Workflow Id。

但是需要注意，一个新的Workflow Execution永远不可能与处于Open状态Workflow Execution有相同的工作流 ID。

Workflow Id Reuse Policy有三个可选值：

- **Allow Duplicate** : 允许使用先前已经Closed的Workflow Execution的Id，无论Closed状态是什么。如果没有指定，这个是默认值。
- **Allow Duplicate Failed Only** : 只有当Workflow Execution的Closed状态不是Completed时，才允许重用Id。
- **Reject Duplicate** : 无论什么情况都不允许重用Id

注意：命名空间内的Workflow Execution是可以设置保留期限的。例如设置为30天，则只能看到近30天内的Workflow Execution。Workflow Id 重用策略也只会对有效期内的Workflow Execution进行Id对比。

Run Id

Run Id 是一个Workflow Execution的全局唯一标识。

Child Workflow

子工作流是由另一个工作流产生的工作流。任何一个工作流（包括子工作流）都可以产生子工作流。

工作流在开启子工作流时，必须等待子工作流创建成功，但是可以选择同步或者异步等待子工作流执行结果。

子工作流上可以设置Parent Close Policy（父关闭策略），来指定当父工作流到达Closed状态时的动作。

如果子工作流使用了Continue-As-New，那么在父工作流眼中，视为一个Workflow Execution。

使用Child Workflow

- 突破工作流执行历史事件的限制

例如，一个Workflow Execution是没有足够的空间来生成100000个Activity执行的，但是如果由一个工作流开启1000个子工作流，每个子工作流生成1000个Activity，就可以实现1000000个Activity的执行。

但是另一方面，子工作流的部分事件，在父工作流里也是要维护的，所以理论上单个父工作流产生不超过1000个子工作流。

- 考虑将子工作流视为独立的服务

子工作流也是一个普通的工作流，由Worker来负责调度执行，而不是父工作流管理，所以子工作流可以作为一个完全独立的服务。这也意味着父子之间不会共享任何的本地状态，和普通工作流一样，只能通过Signal进行通信。

- 用单个工作流来代表单个资源

例如，管理主机升级的工作流，可以为每个主机生成一个子工作流。

Parent Close Policy

这个配置只对子工作流生效。决定了当父工作流到达Closed状态时（Completed, Failed, or Timed out），子工作流会发生什么。有以下三个可选值：

- **Abandon** : 子工作流不会受到任何影响
- **Terminate** : 默认值，子工作流被强制关闭
- **Request Cancel** : 给子工作流发送一个取消请求

Temporal定时任务

Temporal作为一个工作流引擎，定时任务必不可少。

在开启Workflow Execution时，可以指定”cronSchedule“选项，设置一个Cron表达式，来配置Workflow的执行周期。开始workflow后，workflow会立刻创建并处于Running状态，但是不会立刻调度执行，而是有一定的delay，直到到达Cron表达式所指定的下一次时间。

只有在当前执行状态为Completed、Failed或Timed Out后，才会触发下一次运行。也就是说，如果设置了重试策略，并且运行失败或超时，则将首先根据重试策略进行重试，直到运行完成或重试策略已经用尽。如果到达根据 Cron 计划的下一次运行时间，但是由于当前运行仍处于Open状态（包括重试），则服务器将跳过下一次计划的运行。

Cron

Temporal里的Cron表达式比较特殊，只有5位，分别为:

而且需要特别注意，Temporal里的Cron表达式，默认是以UTC时区解析的，国内使用的话，要在你的目标时间上减去8小时之后，再生成Cron表达式。

启动一个定时Workflow之后，什么时候能停下来呢？

一种方式是直接使用API调用terminate命令，定时workflow会立刻终止，状态为Terminated。

另一种方式是等到workflow到达超时限制时间，会自动结束，状态为Timedout。

使用API发送Cancellation请求，只会影响当前的执行，不会取消整个定时。






## ElasticSearch

数据库系统本身要保证实时和强一致性，所以其功能设计上都是为了满足这种一致性需求。比如write ahead log的设计，基于B+树实现的索引和数据组织，以及基于MVCC实现的事务等等。

关系型数据库一般被用于实现OLTP系统，所谓OLTP，在线交易处理（OLTP, Online transaction processing）是指透过信息系统、电脑网络及数据库，以线上交易的方式处理一般即时性的作业数据，和更早期传统数据库系统大量批量的作业方式并不相同。OLTP通常被运用于自动化的数据处理工作，如订单输入、金融业务…等反复性的日常性交易活动。和其相对的是属于决策分析层次的联机分析处理（OLAP）。

在互联网的业务场景中，也有一些实时性要求不高(可以接受多秒的延迟)，但是查询复杂性却很高的场景。举个例子，在电商的WMS系统中，或者在大多数业务场景丰富的CRM或者客服系统中，可能需要提供几十个字段的随意组合查询功能。这种系统的数据维度天生众多

想像一下，如果我们所经营的是一个大型电商，每天有千万级别的订单，那么在这个数据库中查询和建立合适的索引都是一件非常难的事情。

在CRM或客服类系统中，常常有根据关键字进行搜索的需求，大型互联网公司每天会接收数以万计的用户投诉。而考虑到事件溯源，用户的投诉至少要存2~3年。又是千万级甚至上亿的数据。根据关键字进行一次like查询，可能整个MySQL就直接挂掉了。

这时候我们就需要搜索引擎来救场了。

[全文搜索](https://baike.baidu.com/item/全文搜索引擎)属于最常见的需求，开源的 [Elasticsearch](https://www.elastic.co/) （以下简称 Elastic）是目前全文搜索引擎的首选。

它可以快速地储存、搜索和分析海量数据。维基百科、Stack Overflow、Github 都采用它。

Elastic 的底层是开源库 [Lucene](https://lucene.apache.org/)。但是，你没法直接用 Lucene，必须自己写代码去调用它的接口。Elastic 是 Lucene 的封装，提供了 REST API 的操作接口，开箱即用。

安装Elastic

```shell
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.1.zip
$ unzip elasticsearch-5.5.1.zip
$ cd elasticsearch-5.5.1/ 
```

接着，进入解压后的目录，运行下面的命令，启动 Elastic。

```shell
$ ./bin/elasticsearch
```

如果这时[报错](https://github.com/spujadas/elk-docker/issues/92)"max virtual memory areas vm.max*map*count [65530] is too low"，要运行下面的命令。

```shell
$ sudo sysctl -w vm.max_map_count=262144
```

如果一切正常，Elastic 就会在默认的9200端口运行。这时，打开另一个命令行窗口，请求该端口，会得到说明信息。

```shell
$ curl localhost:9200

{
  "name" : "atntrTf",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "tf9250XhQ6ee4h7YI11anA",
  "version" : {
    "number" : "5.5.1",
    "build_hash" : "19c13d0",
    "build_date" : "2017-07-18T20:44:24.823Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.0"
  },
  "tagline" : "You Know, for Search"
}
```

上面代码中，请求9200端口，Elastic 返回一个 JSON 对象，包含当前节点、集群、版本等信息。

按下 Ctrl + C，Elastic 就会停止运行。

默认情况下，Elastic 只允许本机访问，如果需要远程访问，可以修改 Elastic 安装目录的`config/elasticsearch.yml`文件，去掉`network.host`的注释，将它的值改成`0.0.0.0`，然后重新启动 Elastic。

```yaml
network.host: 0.0.0.0
```

上面代码中，设成`0.0.0.0`让任何人都可以访问。线上服务不要这样设置，要设成具体的 IP。

### 基本概念

Node与Cluster

Elastic 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elastic 实例。

单个 Elastic 实例称为一个节点（node）。一组节点构成一个集群（cluster）。

Index

Elastic 会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。

所以，Elastic 数据管理的顶层单位就叫做 Index（索引）。它是单个数据库的同义词。每个 Index （即数据库）的名字必须是小写。

下面的命令可以查看当前节点的所有 Index。

```shell
$ curl -X GET 'http://localhost:9200/_cat/indices?v'
```

Document

Index 里面单条的记录称为 Document（文档）。许多条 Document 构成了一个 Index。

Document 使用 JSON 格式表示，下面是一个例子。

```json
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}
```

同一个 Index 里面的 Document，不要求有相同的结构（scheme），但是最好保持相同，这样有利于提高搜索效率。

Type

Document 可以分组，比如`weather`这个 Index 里面，可以按城市分组（北京和上海），也可以按气候分组（晴天和雨天）。这种分组就叫做 Type，它是虚拟的逻辑分组，用来过滤 Document。

不同的 Type 应该有相似的结构（schema），举例来说，`id`字段不能在这个组是字符串，在另一个组是数值。这是与关系型数据库的表的[一个区别](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html)。性质完全不同的数据（比如`products`和`logs`）应该存成两个 Index，而不是一个 Index 里面的两个 Type（虽然可以做到）。

下面的命令可以列出每个 Index 所包含的 Type。

```shell
$ curl 'localhost:9200/_mapping?pretty=true'
```

Elastic 6.x 版只允许每个 Index 包含一个 Type，7.x 版将会彻底移除 Type。

### 新建和删除index

新建 Index，可以直接向 Elastic 服务器发出 PUT 请求。下面的例子是新建一个名叫`weather`的 Index

```shell
$ curl -X PUT 'localhost:9200/weather'
```

服务器返回一个 JSON 对象，里面的`acknowledged`字段表示操作成功。

```json
{
  "acknowledged":true,
  "shards_acknowledged":true
}
```

然后，我们发出 DELETE 请求，删除这个 Index。

```shell
$ curl -X DELETE 'localhost:9200/weather'
```



### 中文分词设置

首先，安装中文分词插件。这里使用的是 [ik](https://github.com/medcl/elasticsearch-analysis-ik/)，也可以考虑其他插件（比如 [smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)）。

```shell
$ ./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.5.1/elasticsearch-analysis-ik-5.5.1.zip
```

上面代码安装的是5.5.1版的插件，与 Elastic 5.5.1 配合使用。

接着，重新启动 Elastic，就会自动加载这个新安装的插件。

然后，新建一个 Index，指定需要分词的字段。这一步根据数据结构而异，下面的命令只针对本文。基本上，凡是需要搜索的中文字段，都要单独设置一下。

```shell
$ curl -X PUT 'localhost:9200/accounts' -d '
{
  "mappings": {
    "person": {
      "properties": {
        "user": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "title": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "desc": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        }
      }
    }
  }
}'
```

Elastic 的分词器称为 [analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html)。我们对每个字段指定分词器。

```json
"user": {
  "type": "text",
  "analyzer": "ik_max_word",
  "search_analyzer": "ik_max_word"
}
```

`analyzer`是字段文本的分词器，`search_analyzer`是搜索词的分词器。`ik_max_word`分词器是插件`ik`提供的，可以对文本进行最大数量的分词。

### 数据操作

新增记录

向指定的 /Index/Type 发送 PUT 请求，就可以在 Index 里面新增一条记录。比如，向`/accounts/person`发送请求，就可以新增一条人员记录。

```shell
$ curl -X PUT 'localhost:9200/accounts/person/1' -d '
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}' 
```

服务器返回的 JSON 对象，会给出 Index、Type、Id、Version 等信息。

```json
{
  "_index":"accounts",
  "_type":"person",
  "_id":"1",
  "_version":1,
  "result":"created",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":true
}
```

如果你仔细看，会发现请求路径是`/accounts/person/1`，最后的`1`是该条记录的 Id。它不一定是数字，任意字符串（比如`abc`）都可以。

新增记录的时候，也可以不指定 Id，这时要改成 POST 请求。

```shell
$ curl -X POST 'localhost:9200/accounts/person' -d '
{
  "user": "李四",
  "title": "工程师",
  "desc": "系统管理"
}'
```

上面代码中，向`/accounts/person`发出一个 POST 请求，添加一个记录。这时，服务器返回的 JSON 对象里面，`_id`字段就是一个随机字符串。

```json
{
  "_index":"accounts",
  "_type":"person",
  "_id":"AV3qGfrC6jMbsbXb6k1p",
  "_version":1,
  "result":"created",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":true
}
```

注意，如果没有先创建 Index（这个例子是`accounts`），直接执行上面的命令，Elastic 也不会报错，而是直接生成指定的 Index。所以，打字的时候要小心，不要写错 Index 的名称。

查询记录

向`/Index/Type/Id`发出 GET 请求，就可以查看这条记录。

```shell
$ curl 'localhost:9200/accounts/person/1?pretty=true'
```

上面代码请求查看`/accounts/person/1`这条记录，URL 的参数`pretty=true`表示以易读的格式返回。

返回的数据中，`found`字段表示查询成功，`_source`字段返回原始记录。

```json
{
  "_index" : "accounts",
  "_type" : "person",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "user" : "张三",
    "title" : "工程师",
    "desc" : "数据库管理"
  }
}
```

如果 Id 不正确，就查不到数据，`found`字段就是`false`。

```shell
$ curl 'localhost:9200/weather/beijing/abc?pretty=true'

{
  "_index" : "accounts",
  "_type" : "person",
  "_id" : "abc",
  "found" : false
}
```

删除记录

删除记录就是发出 DELETE 请求。

```shell
$ curl -X DELETE 'localhost:9200/accounts/person/1'
```

更新记录

更新记录就是使用 PUT 请求，重新发送一次数据。

```shell
$ curl -X PUT 'localhost:9200/accounts/person/1' -d '
{
    "user" : "张三",
    "title" : "工程师",
    "desc" : "数据库管理，软件开发"
}' 

{
  "_index":"accounts",
  "_type":"person",
  "_id":"1",
  "_version":2,
  "result":"updated",
  "_shards":{"total":2,"successful":1,"failed":0},
  "created":false
}
```

### 数据查询

返回所有记录

使用 GET 方法，直接请求`/Index/Type/_search`，就会返回所有记录。

```shell
$ curl 'localhost:9200/accounts/person/_search'

{
  "took":2,
  "timed_out":false,
  "_shards":{"total":5,"successful":5,"failed":0},
  "hits":{
    "total":2,
    "max_score":1.0,
    "hits":[
      {
        "_index":"accounts",
        "_type":"person",
        "_id":"AV3qGfrC6jMbsbXb6k1p",
        "_score":1.0,
        "_source": {
          "user": "李四",
          "title": "工程师",
          "desc": "系统管理"
        }
      },
      {
        "_index":"accounts",
        "_type":"person",
        "_id":"1",
        "_score":1.0,
        "_source": {
          "user" : "张三",
          "title" : "工程师",
          "desc" : "数据库管理，软件开发"
        }
      }
    ]
  }
}
```

全文搜索

Elastic 的查询非常特别，使用自己的[查询语法](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl.html)，要求 GET 请求带有数据体。

```shell
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件" }}
}'
```

Elastic 默认一次返回10条结果，可以通过`size`字段改变这个设置。

```shell
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理" }},
  "size": 1
}'
```

还可以通过`from`字段，指定查询位置。

```shell
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "管理" }},
  "from": 1,
  "size": 1
}'
```

上面代码指定，从位置1开始（默认是从位置0开始），只返回一条结果。

逻辑运算

如果有多个搜索关键字， Elastic 认为它们是`or`关系。

```shell
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query" : { "match" : { "desc" : "软件 系统" }}
}'
```

如果要执行多个关键词的`and`搜索，必须使用[布尔查询](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-bool-query.html)。

```shell
$ curl 'localhost:9200/accounts/person/_search'  -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "desc": "软件" } },
        { "match": { "desc": "系统" } }
      ]
    }
  }
}'
```

