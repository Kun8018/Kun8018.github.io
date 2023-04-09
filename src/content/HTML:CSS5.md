---
title: HTML/CSS
date: 2020-08-02 21:40:33
categories: 技术博客
tags:
    - Web，IT，HTML，CSS
toc: true
thumbnail: https://cdn.kunkunzhang.top/stylecomponent.png
---

​      css in js以及css的一些动画

<!--more-->

## Css in Js

css-in-js 作为一个理念较新的开发思路，拥有如下几个明显的优缺点。

优点：

1. 无全局样式冲突。就像 js 文件天然支持模块化的好处一样，原生 css 因为没有模块化能力，天然容易导致全局样式污染，如果不是特意用 BEM 方式命名，想要避免冲突就只能借助 css-in-js 了。（css-modules 也一样能做到）
2. 与 js 代码合在一起。天然融合进 js 代码方便模块化管理，使 css 可以与某个局部模块绑定。（css-modules 也一样能做到，只是必须单独拆一个样式文件）
3. 能将 js 变量应用到样式上。虽然 css 变量也能解决这个问题，但不如 css-in-js 那么直观，inline-style 也能解决这个问题，但会产生大量重复的局部样式，且这个优势 css-modules 做不到。

缺点：

1. css-in-js 运行时解析的实现版本增加了运行时性能压力，尤其在 React18 调度机制模式下，存在无法解决的性能问题（运行时插入样式会导致 React 渲染暂停，浏览器解析一遍样式，渲染再继续，然后浏览器又解析一遍样式）。
2. 增加了包体积。相比原生或者 css-modules 方案来说，增加了运行时框架代码 8kb 左右。
3. 让 ReactDevTools 结构变得复杂，因为 css-in-js 会包裹额外的 React 组件层用来实现样式插入。

除了上述缺点外，css-in-js 还有三点深度使用后才能察觉的坑：

1. 多个不同（甚至是相同）版本的 css-in-js 库同时加载时可能导致错误。笔者用 styled-components 就遇到了类似问题，甚至语法会产生不兼容的情况，虽然这些问题都可以被解决，但花费的额外时间需要计算一样，相比 css-in-js 得到的收益是否值得。
2. 样式插入优先级无法自定义，这就导致产生样式覆盖时，业务对样式覆盖的优先级无法产生稳定的预期。class 优先级由 header 定义顺序决定，而非 className 的字符顺序决定，而 header 定义顺序又由资源加载与 css-in-js 插入执行时机决定，导致业务几乎不可能有稳定的样式覆盖顺序。这里产生的问题就是业务代码不断增多的 `!impprtant` 定义。
3. 不同 React 版本的 SSR，css-in-js 需要适配不同的实现，这对框架作者不太友好。

运行时解析，是 css-in-js 方案永远跨不过去的困境，即便对于编译时 css-in-js 方案来说，也免不了在渲染时做额外的逻辑执行拖慢渲染速度

原因是当 React 重渲染组件时，需要重新解析样式定义，并序列化 className，当渲染非常频繁时会导致明显的性能瓶颈，而解决方法是把样式定义抽出来，但这样就损失了第三个优点，即无法读取 js 变量了

 css-in-js 增加了 8~16kb 其实是在强行堆缺点了，除非你的项目只有一行 css 定义。如果我们只考虑传输时的包体积与 HTML 中样式定义数量，而忽略运行时产生的性能负担，那么 css-in-js 在大型项目无疑是最优的。

原因就是 css-in-js 样式是按需插入的，没有渲染的组件就不会插入样式。甚至渲染了的组件也不一定会插入样式，因为 css-in-js 可以对包含相同样式定义的场景做 className 合并，类似于 webpack 打包时，可以把不同模块公共代码抽到一个 chunk 里

## postcss

PostCSS 的主要功能只有两个：第一个就是前面提到的把 CSS 解析成 JavaScript 可以操作的 抽象语法树结构（Abstract Syntax Tree，AST），第二个就是**调用插件**来处理 AST 并得到结果。

PostCSS 一般不单独使用，**而是与已有的构建工具进行集成**。PostCSS 与主流的构建工具，如 Webpack、Grunt 和 Gulp 都可以进行集成。完成集成之后，选择满足功能需求的 PostCSS 插件并进行配置。

安装

```shell
## postcss往往不单独使用
npm install postcss postcss-loader autoprefixer cssnano postcss-cssnext
## 其中autoprefixer是添加前缀的，解决浏览器兼容问题。比如：-ms-transform:rotate(7deg); 
## cssnano是处理压缩的
## postcss-cssnext是另一种css语法
```

配置

```javascript
{
    loader:'postcss-loader',
    options:{
        ident: 'postcss', //说明options里面插件的使用是针对于谁的，我们这里是针对于postcss的
        plugins:[ //这里的插件只是这对于postcss
            require('autoprefixer')() //引入添加前缀的插件,第二个空括号是将该插件执行
        ]
    }
}
```

### css nano





## css module

css module不是将 CSS 改造成编程语言，而是功能很单纯，只加入了局部作用域和模块依赖，这恰恰是网页组件最急需的功能。

因此，CSS Modules 很容易学，因为它的规则少，同时又非常有用，可以保证某个组件的样式，不会影响到其他组件。

CSS Modules 提供各种[插件](https://github.com/css-modules/css-modules/blob/master/docs/get-started.md)，支持不同的构建工具。本文使用的是 Webpack 的[`css-loader`](https://github.com/webpack/css-loader#css-modules)插件，因为它对 CSS Modules 的支持最好，而且很容易使用。

webpack中配置

```javascript
module.exports = {
  entry: __dirname + '/index.js',
  output: {
    publicPath: '/',
    filename: './bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'stage-0', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: "style-loader!css-loader?modules"
      },
    ]
  }
};
```

全局样式

CSS Modules 允许使用`:global(.className)`的语法，声明一个全局规则。凡是这样声明的`class`，都不会被编译成哈希字符串。

CSS Modules 还提供一种显式的局部作用域语法`:local(.className)`，等同于`.className`



继承/导入

在 CSS Modules 中，一个选择器可以继承另一个选择器的规则，这称为"组合"

```css
.className {
  background-color: blue;
}

.title {
  composes: className;
  color: red;
}
```

选择器也可以继承其他CSS文件里面的规则。

```css
.title {
  composes: className from './another.css';
  color: red;
}
```

CSS Modules 支持使用变量，不过需要安装 PostCSS 和 [postcss-modules-values](https://github.com/css-modules/postcss-modules-values)。

安装postcss

```shell
npm install --save postcss-loader postcss-modules-values
```

在webpack中配置

```javascript
var values = require('postcss-modules-values');

module.exports = {
  entry: __dirname + '/index.js',
  output: {
    publicPath: '/',
    filename: './bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'stage-0', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: "style-loader!css-loader?modules!postcss-loader"
      },
    ]
  },
  postcss: [
    values
  ]
};
```

定制hash字符串

`css-loader`默认的哈希算法是`[hash:base64]`，这会将`.title`编译成`._3zyde4l1yATCOkgn-DBWEL`这样的字符串。

在webpack中配置

```javascript
module: {
  loaders: [
    // ...
    {
      test: /\.css$/,
      loader: "style-loader!css-loader?modules&localIdentName=[path][name]---[local]---[hash:base64:5]"
    },
  ]
}
```



## styled-component

styled-components 是一个常用的 css in js 类库。和所有同类型的类库一样，通过 js 赋能解决了原生 css 所不具备的能力，比如变量、循环、函数等。诸如 sass&less 等预处理可以解决部分 css 的局限性，但还是要学习新的语法，而且需要对其编译，其复杂的 webpack 配置也总是让开发者抵触。而 styled-components 很好的解决了这些问题，很适合 React 技术栈的项目开发。

安装

```shell
npm install --save styled-components
```

使用

```javascript
import styled from 'styled-components';

const Wrapper = styled.section`
  margin: 0 auto;
  width: 300px;
  text-align: center;
`;
const Button = styled.button`
  width: 100px;
  color: white;
  background: skyblue;
`;
render(
  <Wrapper>
    <Button>Hello World</Button>
  </Wrapper>
);

```

基于props定制组件

React传入的所有 props 都可以在定义组件时获取到，这样就可以很容易实现组件主题的定制。如果没有 styled-components 的情况下，需要使用组件 style 属性或者定义多个 class 的方式来实现。

```react
const Button = styled.button`
  background: ${props => props.primary ? 'palevioletred' : 'white'};
  color: ${props => props.primary ? 'white' : 'palevioletred'};
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
  </div>
);
```

样式继承

```javascript
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

const TomatoButton = Button.extend`
  color: tomato;
  border-color: tomato;
`;
```

维持其他属性

有时候正在使用第三方库，style-component使用时可以一并引入

```javascript
const Password = styled.input.attrs({
  type: 'password',
})`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

const Button = styled.button.attrs({
  className: 'small',
})`
  background: black;
  color: white;
  cursor: pointer;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid black;
  border-radius: 3px;
`;
```



注入全局

```javascript
import styled, { injectGlobal } from 'styled-components';
injectGlobal`
  @font-face {
    font-family: 'Operator Mono';
    src: url('../fonts/Operator-Mono.ttf');
  }

  body {
    margin: 0;
  }
`;
```

支持动画

```javascript
import { keyframes } from 'styled-components';
const fadeIn = keyframes`
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
`;

const FadeInButton = styled.button`
  animation: 1s ${fadeIn} ease-out;
`;
```



## vanilla-extract

安装

```shell
yarn add @vanilla-extract/css @vanilla-extract/webpack-plugin
```

在webpack中配置

```javascript
const { VanillaExtractPlugin } = require('@vanilla-extract/webpack-plugin')

module.exports = {
  entry: './src/index.js',
  // ....
  plugins: [new VanillaExtractPlugin()]
};
```

使用

```javascript
import { style } from '@vanilla-extract/css';

export const parentClass = style({
  background: 'red',
  ':hover': {
    background: 'blue',
  },
});

export const childClass = style({
  selectors: {
    '&:nth-child(2n)': {
      background: '#fafafa',
    },
    [`${parentClass} &`]: {
      color: 'pink',
    },
  },
});
```

创建样式集合

```javascript
import { styleVariants } from '@vanilla-extract/css';

const base = style({ padding: 12 });

const backgrounds = {
  primary: 'blue',
  secondary: 'aqua'
} as const;

export const variant = styleVariants(
  backgrounds,
  (background) => [base, { background }]
);
```

全局样式

```javascript
import { style, globalStyle } from '@vanilla-extract/css';

export const parentClass = style({});

globalStyle(`${parentClass} > div`, {
  color: 'red'
});

const Demo = () => (
    <div className={parentClass}>
        <Select/>
    </div>
)
```

创建主题

```javascript
// themes.css.ts
import { createTheme } from '@vanilla-extract/css';

export const [themeA, vars] = createTheme({
  color: {
    brand: 'blue'
  },
  font: {
    body: 'arial'
  }
});

export const themeB = createTheme(vars, {
  color: {
    brand: 'pink'
  },
  font: {
    body: 'comic sans ms'
  }
});
```



相比styled-component的优点：

- 零运行时；
- 样式开发走Typescript安全类型；
- style设计职责分离；

### es-build

安装

```shell
npm install @vanilla-extract/css @vanilla-extract/esbuild-plugin
```

Es-build配置

```javascript
const { vanillaExtractPlugin } = require('@vanilla-extract/esbuild-plugin');

require('esbuild').build({
  entryPoints: ['app.ts'],
  bundle: true,
  plugins: [vanillaExtractPlugin()],
  outfile: 'out.js',
}).catch(() => process.exit(1))
```

最好和postcss一起使用

```javascript
const {
  vanillaExtractPlugin
} = require('@vanilla-extract/esbuild-plugin');
const postcss = require('postcss');
const autoprefixer = require('autoprefixer');

async function processCss(css) {
  const result = await postcss([autoprefixer]).process(
    css,
    {
      from: undefined /* suppress source map warning */
    }
  );

  return result.css;
}

require('esbuild')
  .build({
    entryPoints: ['app.ts'],
    bundle: true,
    plugins: [
      vanillaExtractPlugin({
        processCss
      })
    ],
    outfile: 'out.js'
  })
  .catch(() => process.exit(1));
```

### vite

安装

```shell
npm install @vanilla-extract/css @vanilla-extract/vite-plugin
```

使用

```javascript
import { vanillaExtractPlugin } from '@vanilla-extract/vite-plugin';

// vite.config.js
export default {
  plugins: [vanillaExtractPlugin()]
}
```

### Next.js

安装

```shell
npm install @vanilla-extract/css @vanilla-extract/babel-plugin @vanilla-extract/next-plugin
```

安装

```javascript
const {
  createVanillaExtractPlugin
} = require('@vanilla-extract/next-plugin');
const withVanillaExtract = createVanillaExtractPlugin();

/** @type {import('next').NextConfig} */
const nextConfig = {};

module.exports = withVanillaExtract(nextConfig);
```

### css计算

可以在css-in-js中计算css样式的值

```shell
npm install @vanilla-extract/css-utils
```

使用

```javascript
import { calc } from '@vanilla-extract/css-utils';

const styles = {
  height: calc.multiply('var(--grid-unit)', 2)
};
```



## astroturf

零运行时的css in js样式

使用

```javascript
import { stylesheet } from "astroturf";

const height = 2;

const styles = stylesheet`
  .btn {
    appearance: none;
    height: ${height}rem;
    display: inline-block;
    padding: .5rem 1rem;
  }
  .primary {
    color: white:
    border: 1px solid white;
    background-color: taupe;
    &:hover {
      color: taupe:
      border-color: taupe;
      background-color: white;
    }
  }
`;
const Button = ({ primary }) => {
  const button = document.createElement("button");
  button.classList.add(styles.btn, primary && styles.primary);
  return button;
};
```

使用css

```javascript
import React from "react";
import { css } from "astroturf";
const btn = css`
  color: black;
  border: 1px solid black;
  background-color: white;
`;
export default function Button({ children }) {
  return (
    <button
      {...props}
      css={css`
        color: blue;
        border: 1px solid blue;
        padding: 0 1rem;
      `}
    >
      {children}
    </button>
  );
  // return <button className={btn}>{children}</button>;
}
```

使用

```javascript
import * as React from "react";
import { styled } from "astroturf/react";
const Button = styled("button")`
  border: 1px solid transparent;
  &.disabled {
    opacity: 0.6;
  }
  &.variant-primary {
    color: blue;
    border-color: blue;
  }
`;
<Button variant="primary" disabled />;
```



## Linaria

Linaria 是一个 **零运行时** 的JSS 框架，其特点有：

- **将 CSS 纳入到 JS 体系中**，并且这种支持是零成本的！  CSS 相关代码会在编译期被抽出到 CSS 文件中
- 类 Sass 的 CSS 的语法
- 通过使用 CSS 变量，Linaria 支持快速创建动态属性的 React 样式组件
- 使用 CSS sourcemaps **易于定位样式位置**
- 支持 stylint
- 不再需要预处理器，可以**使用 JavaScript 控制 CSS 的逻辑**
- 但是**支持使用预处理器**，比如 Sass 或 PostCSS

Linaria 同时支持 Webpack ， Rollup 和 Sevlte

和 Webpack 配合很容易，只需要将 babel-loader 加上 `linaria/loader` 即可，一定确保 `linaria/loader` 紧挨着且在 babel-loader 之后

```javascript
{
  test: /\.js$/,
  use: [
    { loader: 'babel-loader' },
    {
      loader: 'linaria/loader',
      options: {
        sourceMap: process.env.NODE_ENV !== 'production',
        cacheDirectory: '.linaria-cache', // 缓存所在文件见，默认 .linaria-cache
        extension: '.linaria.css', // CSS 文件处于中间态时的命名，默认 .linaria.css
        preprocessor: 'stylis', // 定义 css 的预处理器，默认为 stylis
      },
    }
  ],
}
```

此外，为了将收集到的样式抽取出来，你需要另外一个 Webpack 插件 `mini-css-extract-plugin` , 执行 `npm i -D css-loader mini-css-extract-plugin` 来安装

然后导入 `mini-css-extract-plugin`

```javascript
{
  test: /\.css$/,
  use: [
    {
      loader: MiniCssExtractPlugin.loader,
      options: {
        hmr: process.env.NODE_ENV !== 'production',
      },
    },
    {
      loader: 'css-loader',
      options: {
        sourceMap: process.env.NODE_ENV !== 'production',
      },
    },
  ],
},
```

### linaria/react

在react中使用

```react
import { css, cx } from 'linaria';

const box = css`
  animation: rotate 1s linear infinite;

  @keyframes rotate {
    { from: 0deg; }
    { to: 360deg; }
  }
`;

const yarn = css`
  color: violet;
`;

const fun = css`
  display: flex;
`;

function App({ isPlaying }) {
  return <Playground className={cx(box, yarn, isPlaying && fun)} />;
}
```

`cx()` 这个函数看着很像一个流行库 `classnames` ，但还是有点区别的， `cx()` 不处理对象

安装linaria/react库

```shell
npm i linaria/react --dev
```

使用

```react
import { styled } from 'linaria/react';

const Card = styled.div`
	border: 1px solid #fff;
  border-radius: 4px;
`

const App = () => {
  return <Card className={cs(active && 'active', className)}>1</Card>
}
```



服务端api

在做 SSR 时我们不仅需要将相应的 HTML 代码进行返回，也需要将 **需要的** 样式代码返回，这就那些 **关键的** 的 CSS 代码，我们可以通过利用 `collect()` 函数来抽离出关键的 CSS 代码

```react
import { collect } from 'linaria/server';

const css = fs.readFileSync('./dist/styles.css', 'utf8');
const html = ReactDOMServer.renderToString(<App />);
const { critical, other } = collect(html, css);

// critical – returns critical CSS for given html
// other – returns the rest of styles
```

`collect()`  会根据元素的 class 属性，将用到的 CSS 代码抽离出来，这样就可以跟随 html 一起返回

需要注意的被抽离出来的 css 代码选择器的顺序会变乱掉，这使得如果你的样式依赖选择器顺序的权重，可能就会出现意料的之外的错误，不过由于 Linaria 生成的 class 命名都是唯一的，所以一般不会出现这个问题，但与其他的库协作时需要注意到这点

Linaria 是基于 CSS 变量的，大部分现代浏览器支持这个特性，但是对于 IE 11 以及以下，是不支持的，所以如果你需要支持 IE 11 ，也许 Linaria 不是你最好的选择

## emotion

安装

```shell
npm install --save @emotion/react
```

使用

```jsx
import { jsx } from '@emotion/react'

let SomeComponent = props => {
  return (
  	<div
      css={{
        color: 'hotpink'
      }}
     	/>
  )
}
```

@emotion/css

```react
import { css } from '@emotion/css';

const customStyle = css`
  margin-bottom: 4px;
`

const App = () => {
	return <p className={labelStyle}>1</p>;
}
```



https://github.com/emotion-js/emotion



## Pico.css

安装

```shell
npm install @picocss/pico
```



## Stitches.css

在react中使用

```shell
npm install @stitches/react
```





## TailWind CSS

Tailwind CSS就是一个使用公用程序类的CSS框架，这个公用类仅仅只是一个CSS样式类，其中包含着各种各样的CSS样式供开发者开箱即用而不需要再编写复杂繁多的CSS，这些CSS样式大多是安全的能让页面看上去更美观。同时它也支持自由扩展，可以在原有的基础类中针对自己的需求进行个性化定制。

Tailwind的优势

- 开箱即用，无需离开HTML即可编写CSS样式，直接使用即可
- 上手容易，学习成本低
- 功能丰富，便于开发者开发
- CSS库成熟，其中包含各种各样已经封装好的CSS样式
- 性能优秀，生产坏境下体积非常小
- 可定制化，在原有CSS库基础上可以进行扩展定制个性化CSS

安装

```shell
yarn add -D tailwindcss@npm:@tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9
```

添加配置文件tailwind.config

tailwind.config.js可以改变tailwindcss的基础配置，以做到让开发者的定制化开发，在配置中加入某些类以便可以在全局中使用

```javascript
const colors = require('tailwindcss/colors')

module.exports = {
  purge: ['./src/**/*.html', './src/**/*.tsx', './src/**/*.ts'],
  darkMode: false, // or 'media' or 'class'
  theme: {
    extend: {
      spacing: {
        1: '1px',
        2: '2px',
        3: '3px',
        4: '4px',
        5: '5px',
        6: '6px',
        7: '7px',
        8: '8px',
        9: '9px',
        10: '10px',
        11: '11px',
        12: '12px',
        13: '13px',
        14: '14px',
        15: '15px',
        16: '16px',
        17: '17px',
        18: '18px',
        19: '19px',
        20: '20px',
        21: '21px',
        22: '22px',
        23: '23px',
        24: '24px',
        25: '25px',
        26: '26px',
        27: '27px',
        28: '28px',
        29: '29px',
        30: '30px',
        31: '31px',
        32: '32px',
        34: '34px',
        36: '36px',
        38: '38px',
        40: '40px',
        44: '44px',
        48: '48px',
        52: '52px',
        56: '56px',
        60: '60px',
      },
    },
    fontSize: {
      xs: '12px',
      base: '14px',
      lg: '16px',
      xl: '18px',
      '2xl': '20px',
      '3xl': '22px',
      '4xl': '24px',
      '5xl': '26px',
      '6xl': '28px',
      '7xl': '30px',
    },
  },
  variants: {
    // 移除响应式版本 https://www.tailwindcss.cn/docs/optimizing-for-production#-7
    appearance: [],
    extend: {},
  },
  plugins: [],
};
```

使用时在组件中开箱即用，不需要import

```tsx
export default function Header() {
  return <div>
  	<div className="relative top-4 bg-red-300"></div>
  </div>
}
```

用tailwind编写class

```css
.header {
  @apply relative top-4 bg-blue-300
}
```

在tsx中引入

```tsx
import './index.css'

export default function Header() {
  return <div>
  	<div classNames="header">header</div>
  </div>
}
```

复用class中的类

```css
.header {
  @apply relative top-4 bg-blue-300
}

.header {
  @apply header;
  @apply mt-3;
  @apply bg-red-300;
}
```

在tsx中引入

```tsx
import './index.css'

export default function Header() {
  return <div>
  	<div classNames="header">header</div>
    <div classNames="header1">header1</div>
  </div>
}
```

### typewind

typewind支持类型安全地使用tailwind，并且是零运行时

如果使用monorepo，可以自定义typewind引入tailwind的配置文件路径

```json
{
    "typewind": {
        "configPath": "./path/to/your/tailwind.config.cjs"
    }
}
```

使用

```react
import { tw } from 'typewind';
 
export default function Button() {
  return (
    <button className={tw.bg_blue_500.text_white.rounded.py_3.px_4}>
      Click Me
    </button>
  );
}

// 输出
// <button className="bg-blue-500 text-white rounded py-3 px-4">Click Me</button>
```



### windi css

**Windi CSS** 是下一代工具优先的 CSS 框架。

如果你已经熟悉了 [Tailwind CSS](https://tailwindcss.com/docs)，可以把 Windi CSS 看作是**按需供应的** Tailwind 替代方案，它为你提供了更快的加载体验，**完美兼容 Tailwind v2.0**，并且拥有很多额外的酷炫功能。

## twin



```react
import React from 'react'
import tw from 'twin.macro'
import { Button, Logo } from './components'

const styles = {
  // Move long class sets out of jsx to keep it scannable
  container: ({ hasBackground }) => [
    tw`flex flex-col items-center justify-center h-screen`,
    hasBackground && tw`bg-gradient-to-b from-electric to-ribbon`,
  ],
}

const App = () => (
  <div css={styles.container({ hasBackground: true })}>
    <div tw="flex flex-col justify-center h-full gap-y-5">
      <Button variant="primary">Submit</Button>
      <Button variant="secondary">Cancel</Button>
      <Button isSmall>Close</Button>
    </div>
    <Logo />
  </div>
)

export default App
```

也可以直接



## purgecss

PurgeCSS 是一个用来删除未使用的 CSS 代码的工具。可以将它作为你的开发流程中的一个环节。 当你构建一个网站时，你可能会决定使用一个 CSS 框架，例如 TailwindCSS、Bootstrap、MaterializeCSS、Foundation 等，但是，你所用到的也只是框架的一小部分而已，大量 CSS 样式并未被使用。

接下来就该 PurgeCSS 上场了。PurgeCSS 通过分析你的内容和 CSS 文件，首先它将 CSS 文件中使用的选择器与内容文件中的选择器进行匹配，然后它会从 CSS 中删除未使用的选择器，从而生成更小的 CSS 文件。

安装

```shell
npm install purgecss --save-dev
```

使用

```javascript
import PurgeCSS from 'purgecss';

const purgeCSSResults = await new PurgeCSS().purge({
  content: ["**/*.html"],
  css: ["**/*.css"],
});
```



 
