---
title: Bun
date: 2022-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，C++
toc: true
thumbnail: 
---

​		新的js运行时

<!--more-->

## 安装

curl

```shell
curl -fsSL https://bun.sh/install | bash
```



　　

## elysiajs



使用

```javascript
import { Elysia } from 'elysia'

new Elysia()
    .get('/', 'Hello World')
    .get('/json', {
        hello: 'world'
    })
    .get('/id/:id', ({ params: { id } }) => id)
    .listen(3000)
```



　

## 

　　

## 

　　

## 

