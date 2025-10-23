---
title: HTML/CSS
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，Html
toc: true
thumbnail: 
---

​	类似于HTML的语言，AMP和Marko

<!--more-->

## serve-static

静态页面服务

https://github.com/expressjs/serve-static



## AMP

AMP（加速移动页面）是一个由Google与Twitter合作开发的开源框架，它提供了一种直接的方式来创建轻量级的网页，以便用户即时使用，获得了极大改善的体验：内容更快，更具吸引力，更易于阅读。

从本质上讲，AMP框架允许我们通过简化HTML和简化的CSS规则来为移动设备构建轻量级体验

### 核心组件

**AMP HTML：**

一个比常规HTML更精简的HTML版本，对可以使用的HTML标签有严格的规范。为确保快速加载页面，AMP上不允许使用某些HTML元素，例如：表单；某些默认标签被AMP标签取代，例如，在AMP HTML代码中，标签< amp-img >代替< img >进行图像集成。

**注：AMP对CSS有限制，只能使用简化版的CSS**

**AMP JS：**

为了确保移动平台上的快速页面加载，AMP限制使用任何Javascript，唯一的例外是AMP脚本。需要使用AMP自己的JavaScript库来负责加载所有网站元素，而所有外部资源都是异步处理的，这意味着渲染过程可以在不受外部影响的情况下进行。

**AMP CACHE：**

通常称为AMP缓存，AMP平台的一个关键组件是其基于代理的内容分发网络（CDN），可提供加速移动页面。

### 优缺点

**优点：**

- 1、内容的加载速度非常快，用很好的移动体验感，提高了参与度和转化率。
- 2、通过移动搜索结果，可以在AMP轮播中突出显示内容。
- 3、减少服务器上的负载，因为AMP CDN缓存并响应大多数搜索结果。

**缺点：**

- 1、JavaScript有限制，用户无法自己创建，所以它可能很难编码。
- 2、没有集成插件，一些效果很难在页面中实现。
- 3、简化了HTML，css有限制，不能很好的自定义网站样式，大多是Google的默认格式。



## Marko

声明式的拓展html文件

可以单个marko文件

```html
class {
  onCreate() {
    this.state = { count: 0 };
  }
  increment() {
    this.state.count++;
  }
}

style {
  .count {
    color: #09c;
    font-size: 3em;
  }
  .press-me {
    padding: 0.5em;
  }
}

<output.count>
  ${state.count}
</output>
<button.press-me on-click('increment')>
  Press me!
</button>
```

也可以拆分，marko、css、js

```html
<output.count>
  ${state.count}
</output>
<button.press-me on-click('increment')>
  Press me!
</button>
```

css文件

```css
.count {
  color: #09c;
  font-size: 3em;
}
.press-me {
  padding: 0.5em;
}
```

js

```javascript
export default {
  onCreate() {
    this.state = { count: 0 };
  },
  increment() {
    this.state.count++;
  }
};
```



## 模版代码

### plop

Plop 是一个小型的命令行工具，专注于帮助开发者自动生成代码片段和文件结构，类似于代码生成器。它可以通过简单的模板定义和 CLI（命令行界面）交互，在项目中快速生成预定义的代码片段，从而提升开发效率。

Plop 的主要功能

1. **模板生成**：通过模板和自定义脚本，生成特定的代码或文件结构。通常用于创建组件、服务、模块等常用代码片段。
2. **快速配置**：使用简单的 JavaScript 配置文件 `plopfile.js` 来定义生成器，避免手动创建重复的代码。
3. **交互式 CLI**：运行 `plop` 后，它会提示输入相关参数，并根据这些参数生成定制的代码。
4. **灵活性**：支持自定义脚本和多种模板格式，适用于不同类型的项目（如 React、Vue 等框架）。

为了帮助开发者简化代码生成和项目中的重复性操作。尤其是在前端开发中，开发者通常会创建大量的类似代码结构（如组件、服务模块、Redux actions 等），这些操作容易出错且耗时。Plop 的设计初衷就是以简单、灵活的方式，帮助开发者快速生成标准化的代码结构和文件，从而减少手动创建的工作量。

Plop 诞生背景与要解决的问题

1. **减少重复性代码创建**：前端和全栈开发项目中，常见的文件和组件需要遵循统一的结构和命名规则，手动创建不仅繁琐，而且容易出错。Plop 能够将这些操作自动化，确保一致性。
2. **提升开发效率**：对于团队而言，代码模板生成工具能帮助快速搭建项目框架、减少开发者在基础文件上的时间投入，使其将精力集中在业务逻辑和核心功能开发上。
3. **支持模板化和定制化**：与传统的文件拷贝方法相比，Plop 的交互式 CLI 和自定义模板系统，能根据用户输入创建定制化文件内容，从而满足更灵活的需求。

在代码生成方面，Plop 的竞品主要有 Yeoman、Hygen、Slush 等。这些工具的核心功能类似，但侧重点略有不同：

1. **Yeoman**：

2. - **特点**：Yeoman 是更大型的项目生成器，包含丰富的生成器插件生态系统，支持从项目模板到文件结构的自动生成。Yeoman 适用于初始化复杂项目。
   - **优缺点**：功能非常强大，但配置复杂，依赖于庞大的插件生态，适合大型项目的初始化，但不如 Plop 轻便。
   - **适用场景**：更适合用作项目初始模板生成，而不是简单的文件或组件生成。

3. **Hygen**：

4. - **特点**：Hygen 是另一个文件生成工具，主要通过模板来快速生成代码，并且支持纯本地运行，无需依赖外部包。
   - **优缺点**：相比 Plop，Hygen 更加偏向脚本式运行，可以用配置文件定义一系列生成操作，并通过命令行运行。但其灵活性不及 Plop，交互性较差。
   - **适用场景**：适用于项目中的静态文件生成或模板代码生成。

5. **Slush**：

6. - **特点**：Slush 是基于 Gulp 的项目生成器工具，以任务流的方式自动化代码生成。
   - **优缺点**：依赖 Gulp，对代码生成流程有较多定制需求的开发者可能会喜欢，但整体配置相对繁琐。
   - **适用场景**：适合复杂任务流的代码生成场景，但不如 Plop 轻量和易上手。

### Handlebars

https://github.com/handlebars-lang/handlebars.js

Handlebars.js 诞生于 2010 年左右，是从 Mustache 模板引擎发展而来的。它的诞生背景与当时前端开发中对模板引擎的需求密切相关，主要是为了解决以下几个问题：

1. **视图逻辑和业务逻辑分离**

在传统的 JavaScript 编程中，动态生成 HTML 通常会混合大量的业务逻辑和 DOM 操作，导致代码复杂、不易维护。Handlebars.js 提供了一种更清晰的方法，将 HTML 模板和 JavaScript 逻辑分开，通过占位符的方式在 HTML 中表示数据，而不直接处理业务逻辑。这种模式更易维护和调试，也符合 MVC（Model-View-Controller）架构的分离原则。

2. **简化 HTML 渲染**

在当时，JavaScript 渲染 HTML 的方式通常涉及大量 DOM 操作，如 `document.createElement`、`innerHTML` 等，写法繁琐，且性能不佳。Handlebars.js 允许开发者将模板和数据简单地合并生成最终的 HTML，无需手动操作 DOM，从而加快了开发速度。

3. **增强 Mustache 功能**

Handlebars.js 是对 Mustache 模板引擎的增强。Mustache 本身是一种逻辑少的模板引擎，遵循“无逻辑模板”原则，但它的功能较为有限。为了更灵活地控制模板渲染逻辑，Handlebars.js 在 Mustache 的基础上引入了 **Helper** 和 **Partial** 的概念，使开发者可以定义自定义函数（Helper）和复用小模块（Partial），让模板的表达能力更强。

4. **减少后端模板渲染的压力**

随着单页应用（SPA）和前后端分离架构的流行，前端承担了更多的视图渲染责任。Handlebars.js 等模板引擎帮助前端在客户端渲染模板，减轻了后端服务器的负担，同时也提升了用户体验，因为无需等待服务器渲染即可更新页面内容。

5. **跨平台适用性**

Handlebars.js 可以在浏览器和 Node.js 环境下使用，方便跨平台开发。无论是前端渲染还是在 Node.js 后端生成 HTML，Handlebars.js 都能胜任。
