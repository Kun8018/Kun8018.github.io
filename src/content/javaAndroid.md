---

title: Android与java语言
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，Android、Java
toc: true
thumbnail: 
---

## 概述

​      Android与java。Android是使用的java的语法，另外还有很多移动端的东西，放在一起，java在spring的部分也会讲到。

​      算法题我会偏向于python和go，说不定也会用java和js写一些，暂时不想用c++，也不会用php（只用它写web）

<!--more-->

## 移动端页面概述

对于移动端开发页面，App页面大致分为四种类型：

聚合类：聚合类多见于App的首页，用于功能入口的聚合展示。聚合类相当于分流的作用，用户打开App，进入首页，再通过首页的各个功能入口进入其他页面

特别地，在设计接口时根据用户的需求和产品功能的优先级进行排列，将优先级高的功能入口排在靠前的位置

列表类：列表页是最常见的页面，可以是列表的形式、表格的形式或者卡片的形式，本质就是展示多条信息供用户选择

根据信息的不同特点选择恰当的展示形式，用户会根据这些维度的信息选择决定要不要进入下一级页面

内容类：内容页是用来展示具体信息的页面，购物app中的商品详情页，读书页中的书籍阅读页，资讯App的内容页等

设计内容页时尽量以内容为中心，将不必要的信息暂时隐去，如果涉及到工具栏，将工具栏放到下方而不是上方

功能类：为了完成某个特定功能而存在的也米娜，常见的有搜索页面、发布状态页面、填写收货信息等页面

将页面分类有利于代码文件的组织，同时设计具体页面时可以加深对页面的理解，设计时心中有数

## JVM

JVM是学习和使用java和android不可避免的技术，

　　

## Hashmap

hashmap是java里用的比较多，node中也可以用

hashmap是双列结构，数组+链表，是一个散列表，数组查询很快，但是添加修改很慢，链表查询慢，但是添加修改容易，hashmap应用两种数据结构保证查找和修改都很快





## kotlin

Kotlin已经成为Android的官方语言，是jetBrains在JVM上新推出的语言，能够与java完美兼容，java是1995年推出的语言，而kotlin有很多新特性。Kotlin最大的优点是简单。

### 语法

代码行不需要；结尾

重要的：在for循环、定义变量类型、声明类等很多场景需要用冒号

实例化场景时不需要new

kotlin有区间和集合的概念



## COLA

**COLA 是 Clean Object-Oriented and Layered Architecture的缩写，代表“整洁面向对象分层架构”**

COLA概述：

**架构**的**意义** 就是 要素结构：

- 要素 是 组成架构的重要元素；
- 结构 是 要素之间的关系。

而 **应用架构**的**意义** 就在于

- 定义一套良好的结构；
- 治理应用复杂度，降低系统熵值；
- 从随心所欲的混乱状态，走向井井有条的有序状态。

COLA架构就是为此而生，其核心职责就是定义良好的应用结构，提供最佳应用架构的最佳实践。通过不断探索，我们发现良好的分层结构，良好的包结构定义，可以帮助我们治理混乱不堪的业务应用系统。

好的应用架构，都遵循一些共同模式，不管是六边形架构、洋葱圈架构、整洁架构、还是COLA架构，**都提倡以业务为核心，解耦外部依赖，分离业务复杂度和技术复杂度等**。

COLA架构区别于这些架构的地方，在于除了思想之外，我们还提供了可落地的工具和实践指导。

为了能够快速创建满足COLA架构的应用，我们提供了两个`archetype`，位于[`cola-archetypes`目录](https://github.com/alibaba/COLA/blob/master/cola-archetypes)下：

1. `cola-archetype-service`：用来创建纯后端服务的`archetype`。
2. `cola-archetype-web`：用来创建`adapter`和后端服务一体的`web`应用`archetype`

还有一些功能组件

| 组件名称                           | 功能                                                  | 依赖                   |
| ---------------------------------- | ----------------------------------------------------- | ---------------------- |
| `cola-component-dto`               | 定义了`DTO`格式，包括分页                             | 无                     |
| `cola-component-exception`         | 定义了异常格式， 主要有`BizException`和`SysException` | 无                     |
| `cola-component-statemachine`      | 状态机组件                                            | 无                     |
| `cola-component-domain-starter`    | `Spring`托管的领域实体组件                            | 无                     |
| `cola-component-catchlog-starter`  | 异常处理和日志组件                                    | `exception`、`dto`组件 |
| `cola-component-extension-starter` | 扩展点组件                                            | 无                     |
| `cola-component-test-container`    | 测试容器组件                                          | 无                     |

创建应用

执行以下命令

```java
mvn archetype:generate \
    -DgroupId=com.alibaba.cola.demo.web \
    -DartifactId=demo-web \
    -Dversion=1.0.0-SNAPSHOT \
    -Dpackage=com.alibaba.demo \
    -DarchetypeArtifactId=cola-framework-archetype-web \
    -DarchetypeGroupId=com.alibaba.cola \
    -DarchetypeVersion=4.3.1
```

运行应用

- 在`项目`目录下运行`mvn install`（如果不想运行测试，可以加上`-DskipTests`参数）。
- 进入`start`目录，执行`mvn spring-boot:run`。
  运行成功的话，可以看到`SpringBoot`启动成功的界面。
- 生成的应用中，已经实现了一个简单的`Rest`请求，可以在浏览器中输入 http://localhost:8080/helloworld 进行测试。

如果要生成不是`web`工程而是`service`工程也类似，执行的是下面的命令

```java
mvn archetype:generate \
    -DgroupId=com.alibaba.cola.demo.service \
    -DartifactId=demo-service \
    -Dversion=1.0.0-SNAPSHOT \
    -Dpackage=com.alibaba.demo \
    -DarchetypeArtifactId=cola-framework-archetype-service \
    -DarchetypeGroupId=com.alibaba.cola \
    -DarchetypeVersion=4.3.1
```



## Android apk手机安装流程

1.把apk文件复制到data/app目录

2.解析apk信息

3.dexopt操作

4.更新权限信息

5.完成安装，发送到Intent.ACTION_PACKAGE_ADDED广播

apk是一个类似与zip的压缩文件，安卓代码最后编译成.dev文件。dexopt操作会优化dex文件，生成.odex文件。

　　

## 

