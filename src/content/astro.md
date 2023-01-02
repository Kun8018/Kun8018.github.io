---
title: astro.js
date: 2020-08-02 21:40:33
categories: 技术博客
tags:
    - IT
toc: true
thumbnail: http://cdn.kunkunzhang.top/gatsbyjs.png
---

　　Astro 是一个现代的静态网站生成工具. 与Next.js、Gatsby.js、vuepress等SSR或者静态网站相比最大的特点是可以实现局部渲染，从而实现高效加载，同时能保证网站seo等，是非常好的静态网站生成工具

<!--more-->   

## Astro的特点

建立一个更快的网站有一个简单的秘诀：更少的JS

不幸的是，现代 Web 开发一直在朝着相反的方向发展——更多。更多的 JavaScript、更多的功能、更多的移动部件，最终需要更多的复杂性来保持一切顺利运行

Astro具有以下特点：

- 自带框架 (BYOF)：使用 React、Svelte、Vue、Preact、Web 组件或仅使用普通的 HTML + JavaScript 构建您的站点。
- 100% 静态 HTML，无 JS： Astro 将您的整个页面呈现为静态 HTML，默认情况下从最终构建中删除所有 JavaScript。
- 按需组件：当交互式组件在页面上需要可见时需要一些 JS，Astro 可以自动对其进行融合。如果用户从未看到它，他们永远不会加载它。
- 功能齐全： Astro 支持 TypeScript、Scoped CSS、CSS Modules、Sass、Tailwind、Markdown、MDX 和任何您喜欢的 npm 包
- 启用 SEO：自动站点地图、RSS 提要、分页和集合消除了 SEO 的痛苦

Astro 的工作方式很像静态站点生成器。如果您曾经使用过 Eleventy、Hugo 或 Jekyll（甚至是像 Rails、Laravel 或 Django 这样的服务器端 Web 框架），那么您应该对 Astro 感到宾至如归。

在 Astro 中，您可以使用来自您最喜欢的 JavaScript Web 框架（React、Svelte、Vue 等）的 UI 组件来构建您的网站。Astro 在构建期间将您的整个站点呈现为静态 HTML。结果是一个完全静态的网站，从最终页面中删除了所有 JavaScript。不需要单一的 JavaScript 应用程序，只需在浏览器中尽可能快地加载静态 HTML，无论您使用多少 UI 组件来生成它。

当然，有时客户端 JavaScript 是不可避免的。图片轮播、购物车和自动完成搜索栏只是需要在浏览器中运行一些 JavaScript 的几个例子。这就是 Astro 真正闪耀的地方：当一个组件需要一些 JavaScript 时，Astro 只加载那个组件（以及任何依赖项）。您网站的其余部分继续作为静态、轻量级的 HTML 存在。

在其他全栈 Web 框架中，如果不使用 JavaScript 加载整个页面，延迟交互性，这种级别的每个组件优化是不可能的。在 Astro 中，这种[部分水合作用](https://addyosmani.com/blog/rehydration/)内置于工具本身中。

您甚至可以[自动推迟组件，](https://codepen.io/jonneal/full/ZELvMvw)使其仅在使用:visible修改器在页面上可见时才加载。

这种 Web [**架构**](https://www.jdon.com/tags/249)的新方法称为[孤岛架构](https://jasonformat.com/islands-architecture/)。我们没有创造这个词，但 Astro 可能已经完善了这项技术。我们相信，HTML-first、JavaScript-only-as-needed 方法是大多数基于内容的网站的最佳解决方案。

Astro 由[Snowpack](https://snowpack.dev/)和[Skypack](https://skypack.dev/)背后的开源开发团队构建，并得到了社区的额外贡献。

Astro 一直是免费的。它是一个在[MIT 许可](https://github.com/snowpackjs/astro/blob/main/LICENSE)下发布的开源项目

## 安装/启动

```shell
# create your project 
mkdir new-project-directory 
cd new-project-directory 
npm init astro 
# install your dependencies 
npm install 
# start the dev server and open your browser 
npm start
```



## 目录

Public:

Pages:

Components: 



## meilisearch



## Islands架构

MPA 有更好的首屏性能，SPA 在后续页面的访问中有更好的性能和体验，但 SPA 也带来了更高的工程复杂度、略差的首屏性能和 SEO。这样就需要在不同的场景中做一些取舍。

不过，MPA 和 SPA 也并不是完全割裂的，两者也是能够有所结合的，比如 SSR/SSG 同构方案就是一个典型的体现，首先框架侧会在服务端生成完整的 HTML 内容，并且同时注入客户端所需要的 SPA 脚本。这样浏览器会拿到完整的 HTML 内容，然后执行客户端的脚本事件的绑定(这个过程也叫 `hydrate`)，后续路由的跳转由 JS 来掌管。当下很多的框架都是采用这样的方案，比如 Next.js、Gatsby、公司内部的 Eden SSR、Modern.js。

但实际上，把 MPA 和 SPA 结合的方案也并不是完美无缺的，主要的问题在于这类方案仍然会下载`全量的客户端 JS `及执行`全量的组件 Hydrate 过程`，造成页面的首屏 TTI 劣化。

我们可以试想对于一个文档类型的站点，其实里面的大多数组件是不需要交互的，主要以静态页面的渲染为主，因此直接采用 MPA 方案是一个比 `MPA + SPA` 更好的一个选择。进一步讲，对于更多的轻交互、重内容的应用场景，MPA 也依然是一个更好的方案。

由于页面中有时仍然不可避免的需要一些交互的逻辑，那放在 MPA 中如何来完成呢？这就是 Islands 架构所要解决的问题。

Islands 架构模型早在 2019 年就被提出来了，并在 2021 年被 Preact 作者`Json Miller` 在 Islnads Architecture 一文中得到推广。这个模型主要用于 SSR (也包括 SSG) 应用，我们知道，在传统的 SSR 应用中，服务端会给浏览器响应完整的 HTML 内容，并在 HTML 中注入一段完整的 JS 脚本用于完成事件的绑定，也就是完成 hydration (注水) 的过程。当注水的过程完成之后，页面也才能真正地能够进行交互。

而对于静态组件，即不可交互的组件，我们可以让其不参与 hydration 过程，直接复用服务端下发的 HTML 内容。

可交互的组件就犹如整个页面中的孤岛(Island)，因此这种模式叫做 Islands 架构。

在 Astro 中，默认所有的组件都是静态组件

这种写法不会在浏览器添加任何的 JS 代码。但有时我们需要在组件中绑定一些交互事件，那么这时就需要`激活孤岛组件`了，在使用组件时加上`client:load`指令即可

Astro 除了支持本身 Astro 语法之外，也支持 Vue、React 等框架，可以通过插件的方式来导入。在构建的时候，Astro 只会打包并注入 Islands 组件的代码，并且在浏览器渲染，分别调用不同框架(Vue、React)的渲染函数完成各个 Islands 组件的 hydrate 过程
