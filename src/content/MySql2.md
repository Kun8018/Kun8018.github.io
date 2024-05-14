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
