---
title: 数据库SQL 
date: 2020-03-02 21:40:33
categories: IT
tags:
    - IT，Web,数据库
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OTjU.png
---

　　数据库有关的知识，学习后端必备。

<!--more-->

## Oracel



## pgbouncer

[数据库](https://cloud.tencent.com/solution/database?from_column=20065&from=20065)连接是一个很关键的有限的昂贵的资源，也容易对数据库造成安全隐患。因此，在程序初始化时，预先创建一定数量的数据库连接，并对其进行集中管理，就构成了数据库连接池，由程序动态地对池中的连接进行申请、使用和释放，既保证了较快的数据库读写速度，又提高了安全可靠性。

数据库连接池

- 从连接池获取/创建可用连接
- 使用完后，把连接归还给连接池
- 在系统关闭前，断开所有连接并释放占用的系统资源

如果不使用数据库连接池，则操作流程如下：

- TCP三次握手建立连接
- [MySQL](https://cloud.tencent.com/product/cdb?from_column=20065&from=20065)认证的三次握手
- SQL执行
- MySQL关闭
- TCP四次挥手关闭连接

使用数据库连接池，其流程如下：

只有第一次访问需要建立连接，之后的访问，复用之前创建的连接，直接执行SQL语句

使用数据库连接池好处：

- 资源重用：避免了频繁的创建、释放连接引起的性能开销，在减少系统消耗的基础上，也增进了系统运行环境的平稳性（减少内存碎片以及数据库临时进程/线程的数量）
- 更快的系统响应速度：数据库连接池在初始化过程中，往往已创建了若干数据库连接于池中备用，此时连接的初始化工作均已完成，对于业务请求处理而言，直接利用现有可用连接，避免了从数据库连接初始化和释放过程的开销，从而缩减了系统整体响应时间
- 统一的连接管理，避免连接数据库连接泄漏：在较为完备的数据库连接池实现中，可根据预先的连接占用超时设定，强制收回被占用连接。从而避免了常规数据库连接操作中可能出现的资源泄漏。

连接池：被动分配，用完放回。

线程池：主动干活，有任务到来，线程不断取出任务执行。

## proxysql

https://github.com/sysown/proxysql

## Pg for K8s



https://portworx.com/blog/choosing-a-kubernetes-operator-for-postgresql/



https://github.com/CrunchyData/postgres-operator/



https://github.com/zalando/postgres-operator



https://github.com/ankane/pgsync



## PostGreSQL

https://www.sjkjc.com/postgresql/drop-index/

Ubuntu

```shell
apt-get install postgresql-client
apt-get install postgresql
## 图形化界面安装和启动
apt-get install pgadmin4
pgadmin4
```

启动

```shell
/etc/init.d/postgresql start
```

切换到数据库自动创建的用户

```shell
su - postgres
## 进入数据库，要求输入密码
psql
```

基本操作

```sql
## 创建用户
create user test with password 'test';
## 创建数据库
create database testdb owner test;
## 授权
grant all privileges on database testdb to test
## 退出
\q
```

### 基本类型





## readyset

https://github.com/readysettech/readyset#tutorial

缓存数据库，对于pg和mysql



## sql工具

帮忙生成sql工具：https://www.sqlfather.com/

快速熟悉pg库：https://www.crunchydata.com/developers/tutorials
