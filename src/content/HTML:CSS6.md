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

