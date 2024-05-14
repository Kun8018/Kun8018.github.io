---
title: Deno
date: 2020-03-18 21:40:33
categories: 技术博客
tags:
    - 技术，Robotic，IT
toc: true
thumbnail: http://cdn.kunkunzhang.top/hello-deno.png
---

　　一个新的JavaScript后端

<!--more-->



## web-view

github：https://github.com/Boscop/web-view

This library provides a Rust binding to the original implementation of [webview](https://github.com/zserge/webview), a tiny cross-platform library to render web-based GUIs as desktop applications.



## JSR

Deno 搞了一个叫 [JSR](https://link.zhihu.com/?target=https%3A//jsr.io/) 的东西，全称是 JavaScript Registry，一个新的 JS 源，像 npm 一样。

为什么有了 npm 还要做 JSR？

- ESM 模块现在已经成为标准，CommonJS 正在逐渐被取代。
- 越来越多的 Node 和浏览器之外的 JS 运行时出现，以 Node 为中心的 Registry 将会不再适用。
- TS 目前已经成为事实标准，需要一个对 TS 支持更好的现代化 Registry。

JSR 现在的特点：

- 原生 TS 支持。开发者可以直接上传 TS 代码，对于像 Deno 这样原生支持 TS 的运行时，会直接使用 TS 文件。对于 Node 这样的缺乏 TS 原生支持的环境，JSR 会将代码自动转换为 JS 并生成 `.d.ts`，不需要开发者配置额外的构建步骤。同时在 JSR 的网站上自动根据 TS 类型和注释生成文档。
- 只支持 ESM。不支持 CommonJS。
- 跨运行时支持。JSR 不是为 Node 或 Deno 而生，而是为所有 JS 运行时而生。
- 兼容 npm。JSR 做了个 npm 兼容层，可以很方便地在 Node 项目中使用。

https://zhuanlan.zhihu.com/p/683025584

## Deno学习清单

deno包下载排行：https://yoshixmk.github.io/deno-x-ranking/

deno钻研之术：https://deno-tutorial.js.org/

deno中文手册：https://manual.deno.js.cn/

deno中文社区：https://deno.js.cn/

deno狂热爱好者：https://github.com/hylerrix

demo各种代码：https://axihe.com/edu/deno/home.html



## Fresh

Fresh 属于 **Web 全栈开发框架**。是不是对于这个词非常眼熟呢？相信你已经想到了，像现在大名鼎鼎的 Next.js 以及新出的 Remix 都是走的这个路线。那么作为 Next.js 和 Remix 的竞品， Fresh 有哪些值得一提的亮点，或者说有哪些差异点呢？主要包括如下的几个方面：

首先，Fresh 基于 Deno 运行时，由 Deno 原班人马开发，享有 Deno 一系列工具链和生态的优势，比如内置的测试工具、支持 http import 等等。

其次是渲染性能方面，Fresh 整体采用 Islands SSR 架构(之前介绍的 Astro 也是类似)，实现了客户端按需 Hydration，有一定的渲染性能优势。

当然，还有一个比较出色的点是构建层做到了 Bundle-less，即应用代码不需要打包即可直接部署上线，后文会介绍这部分的具体实现。

最后，不同于 Next.js 和 Remix，Fresh 的前端渲染层由 Preact 完成，包括 Islands 架构的实现也是基于 Preact，且不支持其它前端框架。



https://mp.weixin.qq.com/s?__biz=MzU0MTU4OTU2MA==&mid=2247490454&idx=1&sn=c0d516b0a3c64cc3eefb8aca9f305ea0&scene=21#wechat_redirect

## aleph.js



github:https://www.github.com/alephjs/aleph.js

文档：https://alephjs.org