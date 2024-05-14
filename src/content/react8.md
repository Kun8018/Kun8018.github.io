---
title: React（八）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/preact.jpeg

---

​      基于React的衍生框架

<!--more-->

## create-react-app

### react-app-rewired

安装

```shell
npm install react-app-rewired --save-dev
```

在根目录下创建一个config-overrides.js文件

```javascript
/* config-overrides.js */

module.exports = function override(config, env) {
  //do stuff with the webpack config...
  return config;
}
```

修改package.json脚本

```json
  /* package.json */

"scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test",
+   "test": "react-app-rewired test",
    "eject": "react-scripts eject"
}
```

配置文件

```javascript
module.exports = {
  // The Webpack config to use when compiling your react app for development or production.
  webpack: function(config, env) {
    // ...add your webpack config
    return config;
  },
  // The Jest config to use when running your jest tests - note that the normal rewires do not
  // work here.
  jest: function(config) {
    // ...add your jest config customisation...
    // Example: enable/disable some tests based on environment variables in the .env file.
    if (!config.testPathIgnorePatterns) {
      config.testPathIgnorePatterns = [];
    }
    if (!process.env.RUN_COMPONENT_TESTS) {
      config.testPathIgnorePatterns.push('<rootDir>/src/components/**/*.test.js');
    }
    if (!process.env.RUN_REDUCER_TESTS) {
      config.testPathIgnorePatterns.push('<rootDir>/src/reducers/**/*.test.js');
    }
    return config;
  },
  // The function to use to create a webpack dev server configuration when running the development
  // server with 'npm run start' or 'yarn start'.
  // Example: set the dev server to use a specific certificate in https.
  devServer: function(configFunction) {
    // Return the replacement function for create-react-app to use to generate the Webpack
    // Development Server config. "configFunction" is the function that would normally have
    // been used to generate the Webpack Development server config - you can use it to create
    // a starting configuration to then modify instead of having to create a config from scratch.
    return function(proxy, allowedHost) {
      // Create the default config by calling configFunction with the proxy/allowedHost parameters
      const config = configFunction(proxy, allowedHost);

      // Change the https certificate options to match your certificate, using the .env file to
      // set the file paths & passphrase.
      const fs = require('fs');
      config.https = {
        key: fs.readFileSync(process.env.REACT_HTTPS_KEY, 'utf8'),
        cert: fs.readFileSync(process.env.REACT_HTTPS_CERT, 'utf8'),
        ca: fs.readFileSync(process.env.REACT_HTTPS_CA, 'utf8'),
        passphrase: process.env.REACT_HTTPS_PASS
      };

      // Return your customised Webpack Development Server config.
      return config;
    };
  },
  // The paths config to use when compiling your react app for development or production.
  paths: function(paths, env) {
    // ...add your paths config
    return paths;
  },
}
```

添加多页面入口

```shell
npm install react-app-rewire-multiple-entry --save-dev
```

在config-overrides.js配置

```javascript
const { override, overrideDevServer } = require('customize-cra');

const multipleEntry = require('react-app-rewire-multiple-entry')([{
    entry: 'src/pages/options.tsx',
    template: 'public/options.html',
    outPath: '/options.html',
}]);

const addEntry = () => config => {

    multipleEntry.addMultiEntry(config);
    return config;
};

const addEntryProxy = () => (configFunction) => {
    multipleEntry.addEntryProxy(configFunction);
    return configFunction;
}

module.exports = {
    webpack: override(
        addEntry(),
    ),
    devServer: overrideDevServer(
        addEntryProxy(),
    )
}
```



### CRACO

**C**reate **R**eact **A**pp **C**onfiguration **O**verride修改cra配置的一个包

安装

```shell
npm i @craco/craco
```

在根目录下创建一个craco.config.js并且修改package.json

```json
"scripts": {
-   "start": "react-scripts start",
+   "start": "craco start",
-   "build": "react-scripts build",
+   "build": "craco build"
-   "test": "react-scripts test",
+   "test": "craco test"
}
```

配置文件可以是`craco.config.ts`, `craco.config.js`, `.cracorc.ts`, `.cracorc.js` 或者 `.cracorc`

```javascript
const { when, whenDev } = require("@craco/craco");

module.exports = {
    eslint: {
        mode: ESLINT_MODES.file,
        configure: {
            formatter: when(process.env.NODE_ENV === "CI", require("eslint-formatter-vso"))
        }
    },
    webpack: {
        plugins: [
            new ConfigWebpackPlugin(),
            ...whenDev(() => [new CircularDependencyPlugin()], [])
        ]
    }
};
```

配置jest

```javascript
/* jest.config.js */

const { createJestConfig } = require("@craco/craco");

const cracoConfig = require("./craco.config.js");
const jestConfig = createJestConfig(cracoConfig);

module.exports = jestConfig;
```

## superplate

https://github.com/pankod/superplate



## Remix.js

Remix由 React Router 原班团队打造，基于 TypeScript 与 React，内建 React Router V6 特性的全栈 Web 框架 Remix 正式开源。

Remix 开源之后可以说是在 React 全栈框架领域激起千层浪，绝对可以算是 Next.js 的强劲对手。Remix 的特性如下：

- 追求速度，然后是用户体验(UX)，支持任何 SSR/SSG 等
- 基于 Web 基础技术，如 HTML/CSS 与 HTTP 以及 Web Fecth API，在绝大部分情况可以不依赖于 JavaScript 运行，所以可以运行在任何环境下，如 Web Browser、Cloudflare Workers、Serverless 或者 Node.js 等
- 客户端与服务端一致的开发体验，客户端代码与服务端代码写在一个文件里，无缝进行数据交互，同时基于 TypeScript，类型定义可以跨客户端与服务端共用
- 内建文件即路由、动态路由、嵌套路由、资源路由等
- 干掉 Loading、骨架屏等任何加载状态，页面中所有资源都可以预加载(Prefetch)，页面几乎可以立即加载
- 告别以往瀑布式(Waterfall)的数据获取方式，数据获取在服务端并行(Parallel)获取，生成完整 HTML 文档，类似 React 的并发特性
- 提供开发网页需要所有状态，开箱即用；提供所有需要使用的组件，包括 <Links> 、<Link>、 <Meta> 、<Form> 、<Script/> ，用于处理元信息、脚本、CSS、路由和表单相关的内容
- 内建错误处理，针对非预期错误处理的 <ErrorBoundary> 和开发者抛出错误处理的 <CatchBoundary>

### 路由

Remix 提供基于文件的路由，将读取数据、操作数据和渲染数据的逻辑都写在同一个路由文件里，方便一致性处理，这样可以跨客户端和服务端逻辑共享同一套类型定义。



## Nextjs

Https://juejin.cn/post/6844904017487724557

`Next.js`是一个基于`React`的一个服务端渲染简约框架。它使用`React`语法，可以很好的实现代码的模块化，有利于代码的开发和维护

Next的优点：

- 默认服务端渲染模式，以文件系统为基础的客户端路由
- 代码自动分隔使页面加载更快
- 以页面为基础的简洁的客户端路由
- 以`webpack`的热替换为基础的开发环境
- 使用`React`的`JSX`和`ES6`的`module`，模块化和维护更方便
- 可以运行在`Express`和其他`Node.js`的`HTTP` 服务器上
- 可以定制化专属的`babel`和`webpack`配置

创建next项目

```shell
npm install --save react react-dom next
```

`Next.js`是从服务器生成页面，再返回给前端展示。`Next.js`默认从 `pages` 目录下取页面进行渲染返回给前端展示，并默认取 `pages/index.js` 作为系统的首页进行展示。注意，`pages` 是默认存放页面的目录，路由的根路径也是`pages`目录

在pages目录下创建indexjs

```javascript
// next-Link用于引入文件
import Link from 'next/link'

const Index = () => (
  <div>
    <Link href="/about">
      <a>About Page</a>
    </Link>
    <p>Hello Next.js</p>
  </div>
)

export default Index
```

### 页面（pages）

在 Next.js 中，一个 **page（页面）** 就是一个从 `.js`、`jsx`、`.ts` 或 `.tsx` 文件导出（export）

这些文件存放在 `pages` 目录下。每个 page（页面）都使用其文件名作为路由（route）

Next.js 支持具有动态路由的 pages（页面）。例如，如果你创建了一个命名为 `pages/posts/[id].js` 的文件，那么就可以通过 `posts/1`、`posts/2` 等类似的路径进行访问

预渲染

默认情况下，Next.js 将 **预渲染** 每个 page（页面）。这意味着 Next.js 会预先为每个页面生成 HTML 文件，而不是由客户端 JavaScript 来完成。预渲染可以带来更好的性能和 SEO 效果

每个生成的 HTML 文件都与该页面所需的最少 JavaScript 代码相关联。当浏览器加载一个 page（页面）时，其 JavaScript 代码将运行并使页面完全具有交互性。（此过程称为 *水合（hydration）*。）

Next.js 具有两种形式的预渲染： **静态生成（Static Generation）** 和 **服务器端渲染（Server-side Rendering）**。这两种方式的不同之处在于为 page（页面）生成 HTML 页面的 **时机** 。

- [**静态生成 （推荐）**](https://www.nextjs.cn/docs/basic-features/pages#static-generation-recommended)：HTML 在 **构建时** 生成，并在每次页面请求（request）时重用。
- [**服务器端渲染**](https://www.nextjs.cn/docs/basic-features/pages#server-side-rendering)：在 **每次页面请求（request）时** 重新生成 HTML。

重要的是，Next.js 允许你为每个页面 **选择** 预渲染的方式。你可以创建一个 “混合渲染” 的 Next.js 应用程序：对大多数页面使用“静态生成”，同时对其它页面使用“服务器端渲染”。

出于性能考虑，相对服务器端渲染，我们更 **推荐** 使用 **静态生成** 。 CDN 可以在没有额外配置的情况下缓存静态生成的页面以提高性能。但是，在某些情况下，服务器端渲染可能是唯一的选择。

你还可以将 **客户端渲染** 与静态生成或服务器端渲染一起使用。这意味着页面的某些部分可以完全由客户端 JavaScript 呈现。

如果一个页面使用了 **静态生成**，在 **构建时（build time）** 将生成此页面对应的 HTML 文件 。这意味着在生产环境中，运行 `next build` 时将生成该页面对应的 HTML 文件。然后，此 HTML 文件将在每个页面请求时被重用，还可以被 CDN 缓存。

在 Next.js 中，你可以静态生成 **带有或不带有数据** 的页面。

不需要获取数据的静态页面

默认情况下，Next.js 使用 “静态生成” 来预渲染页面但不涉及获取数据。

此页面在预渲染时不需要获取任何外部数据。在这种情况下，Next.js 只需在构建时为每个页面生成一个 HTML 文件即可。

需要获取数据的静态生成

某些页面需要获取外部数据以进行预渲染。有两种情况，一种或两种都可能适用。在每种情况下，你都可以使用 Next.js 所提供的以下函数：

1. 您的页面 **内容** 取决于外部数据：使用 `getStaticProps`。
2. 你的页面 **paths（路径）** 取决于外部数据：使用 `getStaticPaths` （通常还要同时使用 `getStaticProps`）。



### 中间件

在根目录下创建一个middleware.ts文件

```javascript
import { NextResponse } from 'next/server'

export function middleware() {
  // Store the response so we can modify its headers
  const response = NextResponse.next()

  // Set custom header
  response.headers.set('x-modified-edge', 'true')

  // Return response
  return response
}
```

在pages目录下创建一个_middleware.ts，那么所有的路由都会执行这个中间件

```javascript
// pages/_middleware.ts

import type { NextFetchEvent, NextRequest } from 'next/server'

export function middleware(req: NextRequest, ev: NextFetchEvent) {
  return new Response('Hello, world!')
}
```

如果在pages下面的子页面创建了_middleware的中间件，那么中间件会按照目录的层级之行

```typescript
- package.json
- /pages
    index.tsx
    - /about
      _middleware.ts # Will run first
      about.tsx
      - /teams
        _middleware.ts # Will run second
        teams.tsx
```



### 多页面



### 使用redux



### 路由遮盖

`Next.js`上提供了一个独特的特性：路由遮盖（Route Masking）。它可以使得在浏览器上显示的是路由`A`，而`App`内部真正的路由是`B`。这个特性可以让我们来设置一些比较简洁的路由显示在页面，而系统背后是使用一个带参数的路由。比如上面的例子中，地址栏中显示的是 `http://localhost:3000/post?title=Hello%20Next.js` ，这个地址含有一个`title`参数，看着很不整洁。下面我们就用`Next.js`来改造路由，使用路由遮盖来创建一个更加简洁的路由地址。比如我们将该地址改造成 `http://localhost:3000/p/hello-nextjs

### 自定义根组件

在page下面创建_app.js，能覆盖默认的appjs

```javascript
import Navbar from './navbar'
import Footer from './footer'

export default function Layout({ children }) {
  return (
    <>
      <Navbar />
      <main>{children}</main>
      <Footer />
    </>
  )
}

// pages/_app.js

import Layout from '../components/layout'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  )
}
```

### next-seo

在next项目中添加seo

安装

```shell
npm install next-seo
```

添加seo

```javascript
import { NextSeo } from 'next-seo';

const Page = () => (
  <>
    <NextSeo
      title="Using More of Config"
      description="This example uses more of the available config options."
      canonical="https://www.canonical.ie/"
      openGraph={{
        url: 'https://www.url.ie/a',
        title: 'Open Graph Title',
        description: 'Open Graph Description',
        images: [
          {
            url: 'https://www.example.ie/og-image-01.jpg',
            width: 800,
            height: 600,
            alt: 'Og Image Alt',
            type: 'image/jpeg',
          },
          {
            url: 'https://www.example.ie/og-image-02.jpg',
            width: 900,
            height: 800,
            alt: 'Og Image Alt Second',
            type: 'image/jpeg',
          },
          { url: 'https://www.example.ie/og-image-03.jpg' },
          { url: 'https://www.example.ie/og-image-04.jpg' },
        ],
        site_name: 'SiteName',
      }}
      twitter={{
        handle: '@handle',
        site: '@site',
        cardType: 'summary_large_image',
      }}
    />
    <p>SEO Added to Page</p>
  </>
);

export default Page;
```

可以在app.js中添加默认的seo属性，没有设置seo的page使用默认的seo

```javascript
import App, { Container } from 'next/app';
import { DefaultSeo } from 'next-seo';

// import your default seo configuration
import SEO from '../next-seo.config';

export default class MyApp extends App {
  render() {
    const { Component, pageProps } = this.props;
    return (
      <Container>
        <DefaultSeo
          openGraph={{
            type: 'website',
            locale: 'en_IE',
            url: 'https://www.url.ie/',
            site_name: 'SiteName',
          }}
          twitter={{
            handle: '@handle',
            site: '@site',
            cardType: 'summary_large_image',
          }}
        />
        <Component {...pageProps} />
      </Container>
    );
  }
}
```

### 自定义babel

在应用顶层创建一个.babelrc或者babel.config.js文件，

```javascript
{
  "presets": [
    [
      "next/babel",
      {
        "preset-env": {},
        "transform-runtime": {},
        "styled-jsx": {},
        "class-properties": {}
      }
    ]
  ],
  "plugins": []
}
```



### SSR

**getInitialProps**是在渲染页面之前就会运行的API。 如果该路径下包含该请求，则执行该请求，并将所需的数据作为**props**传递给页面。 (实际上有时会有发送日志等不影响HTML的副作用。 ）

**getInitialProps**只能在**pages**文件夹内的文件中使用。直接访问后，getInitialProps将在服务器端运行。 另一方面，使用next/link进行客户端路由时，在客户端执行。 因此，建议使用isomorphic-unfetch等fetch库

**getStaticProps**是用于在构建时预先执行**getInitialProps**进行的处理并预先生成静态文件的API。 不会在客户端上运行。 始终在服务器端运行。

getStaticPaths用于在使用动态路由时生成静态文件。

```react
import fetch from 'node-fetch'

function Zeit({ name, stars }) {
  return <div>{name} stars: {stars}</div>
}

// 首先执行。 返回路径以使用数组进行预构建。
export async function getStaticPaths() {
  // zeit获取30个由API管理的存储库
  const res = await fetch('https://api.github.com/orgs/zeit/repos')
  const repos = await res.json()
  // 存储库名称的路径
  const paths = repos.map(repo => `/zeit/${repo.name}`)
  return { paths, fallback: false }
}

// 接收带有路由信息的参数
export async function getStaticProps({ params }) {
  // 对应于文件名zeit/[name].js
  const name = params.name
  const res = await fetch(`https://api.github.com/repos/zeit/${name}`)
  const json = await res.json()
  const stars = json.stargazers_count

  return { props: { name, stars } }
}

export default Zeit
```



### @vercel/og

将html、css转换为图片

```javascript
// /pages/api/og.tsx

import { ImageResponse } from '@vercel/og';

export const config = {
  runtime: 'experimental-edge',
};

export default function () {
  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 128,
          background: 'white',
          width: '100%',
          height: '100%',
          display: 'flex',
          textAlign: 'center',
          alignItems: 'center',
          justifyContent: 'center',
        }}
      >
        Hello world!
      </div>
    ),
    {
      width: 1200,
      height: 600,
    },
  );
}
```

node使用satori生成图片

```react
// api.jsx
import satori from 'satori'

const svg = await satori(
  <div style={{ color: 'black' }}>hello, world</div>,
  {
    width: 600,
    height: 400,
    fonts: [
      {
        name: 'Roboto',
        data: robotoArrayBuffer,
        weight: 400,
        style: 'normal',
      },
    ],
  },
)
```

### next.config.js

```javascript
module.exports = {
  webpack: (config, { buildId, dev, isServer, defaultLoaders, webpack }) => {
    // 自定义输出目录
    distDir: 'build',
    // 重定向
    async redirects() {
      return [
        {
          source: '/about',
          destination: '/',
          permanent: true,
        },
      ]
    },
    // 重写路径
    async rewrites() {
      return [
        {
          source: '/about',
          destination: '/',
        },
      ]
    },
    // 环境变量
    env: {
     customKey: 'my-value',
  	},
    // 自定义webpack配置
    config.module.rules.push({
      test: /\.mdx/,
      use: [
        options.defaultLoaders.babel,
        {
          loader: '@mdx-js/loader',
          options: pluginOptions.options,
        },
      ],
    })
    // Important: return the modified config
    return config
  },
}
```



### 部署

`Next.js` 项目的部署，需要一个 `Node.js`的服务器，可以选择 `Express`, `Koa`或其他 `Nodejs` 的Web服务器。本文中以 `Express` 为例来部署 `Next` 项目。

### Code Hike

在next中使用

```shell
npm install @next/mdx @mdx-js/loader @code-hike/mdx
```

在next.config.js中使用

```javascript
const theme = require("shiki/themes/nord.json")
const {
  remarkCodeHike,
} = require("@code-hike/mdx")

const withMDX = require("@next/mdx")({
  extension: /\.mdx?$/,
  options: {
    remarkPlugins: [
      [remarkCodeHike, { theme }]
    ],
  },
})

module.exports = withMDX({
  pageExtensions: [
    "ts", "tsx", "js", 
    "jsx", "md", "mdx"
  ],
})
```

在项目的根组件中引入code-hike的样式

```javascript
import "@code-hike/mdx/dist/index.css"
```

在项目中mdx中使用

````markdown
# Hello

Lorem ipsum dolor sit amet.

```python hello.py
print("Rendered with Code Hike")
```

Lorem ipsum dolor sit amet.
````

https://codehike.org/docs/configuration
#### shiki

语法高亮的npm包

安装

```shell
npm i shiki
```

使用

```javascript
const shiki = require('shiki')

shiki
  .getHighlighter({
    theme: 'nord'
  })
  .then(highlighter => {
    console.log(highlighter.codeToHtml(`console.log('shiki');`, { lang: 'js' }))
  })

// <pre class="shiki nord" style="background-color: #2e3440"><code>
//   <!-- Highlighted Code -->
// </code></pre>
```

https://github.com/shikijs/shiki

## T3

T3是一个类型安全的全栈nextjs app

安装

```shell
npm create t3-app@latest
```



 ## Relay

基于react和Graphql的react客户端框架

首先创建一个基于create-react-app的文件夹

```shell
# NPM
npx create-react-app your-app-name
# Yarn
yarn create react-app your-app-name
```

然后安装relay包，就可以使用基于graphQL的获取数据方式了

```shell
# NPM Users
npm install --save relay-runtime react-relay
npm install --save-dev relay-compiler babel-plugin-relay
```

修改package.json

```shell
// your-app-name/package.json
{
  ...
  "scripts": {
    ...
    "start": "yarn run relay && react-scripts start",
    "build": "yarn run relay && react-scripts build",
    "relay": "yarn run relay-compiler"
    ...
  },
  "relay": {
    "src": "./src/",
    "schema": "./schema.graphql",
    "language": "javascript"
  }
  ...
}
```

创建一个环境配置文件

```react
// your-app-name/src/RelayEnvironment.js
import {Environment, Network, RecordSource, Store} from 'relay-runtime';
import fetchGraphQL from './fetchGraphQL';

// Relay passes a "params" object with the query name and text. So we define a helper function
// to call our fetchGraphQL utility with params.text.
async function fetchRelay(params, variables) {
  console.log(`fetching query ${params.name} with ${JSON.stringify(variables)}`);
  return fetchGraphQL(params.text, variables);
}

// Export a singleton instance of Relay Environment configured with our network function:
export default new Environment({
  network: Network.create(fetchRelay),
  store: new Store(new RecordSource()),
});
```



在组件中使用

```react
import React from 'react';
import './App.css';
import graphql from 'babel-plugin-relay/macro';
import {
  RelayEnvironmentProvider,
  loadQuery,
  usePreloadedQuery,
} from 'react-relay/hooks';
import RelayEnvironment from './RelayEnvironment';

const { Suspense } = React;

// Define a query
const RepositoryNameQuery = graphql`
  query AppRepositoryNameQuery {
    repository(owner: "facebook", name: "relay") {
      name
    }
  }
`;

const preloadedQuery = loadQuery(RelayEnvironment, RepositoryNameQuery, {
  /* query variables */
});

function App(props) {
  const data = usePreloadedQuery(RepositoryNameQuery, props.preloadedQuery);

  return (
    <div className="App">
      <header className="App-header">
        <p>{data.repository.name}</p>
      </header>
    </div>
  );
}
```



## Dvajs

dva 首先是一个基于 redux 和 redux-saga的数据流方案，然后为了简化开发体验，dva 还额外内置了 react-router和 fetch，所以也可以理解为一个轻量级的应用框架。

dva把redux的action、reducer、createActions、actionType等不同目录的文件组织在一个modle文件中。

安装

```shell
npm install dva-cli@next -g
```

创建项目

```shell
dva new myapp
```

进入目录，运行

```shell
npm start
```



## blitz.js

安装

```shell
npm install -g blitz
```

创建项目

```shell
blitz new AppName
cd 
```



## Taxonomy

全栈开发app



## Umijs

安装

```shell
npm install -g umi
```

Umi 中约定 `src/global.css` 为全局样式，如果存在此文件，会被自动引入到入口文件最前面

比如用于覆盖样式，

```less
.ant-select-selection {
  max-height: 51px;
  overflow: auto;
}
```

Umi 会自动识别 CSS Modules 的使用，你把他当做 CSS Modules 用时才是 CSS Modules

```tsx
// CSS Modules
import styles from './foo.css';

// 非 CSS Modules
import './foo.css';
```

Umi 内置支持 less，不支持 sass 和 stylus，但如果有需求，可以通过 chainWebpack 配置或者 umi 插件的形式支持

MFSU

mfsu 是一种基于 webpack5 新特性 Module Federation 的打包提速方案。核心原理是将应用的依赖构建为一个 Module Federation 的 remote 应用，以免去应用热更新时对依赖的编译。

因此，开启 mfsu 可以大幅减少热更新所需的时间。在生产模式，也可以通过提前编译依赖，大幅提升部署效率。

### 开发阶段

1. 初始化一个 umi 应用。
2. 在 config.ts 中添加 `mfsu:{}`。
3. `umi dev` 启动项目。在构建依赖时，会出现 MFSU 的进度条，此时应用可能会被挂起或显示依赖不存在，请稍等。
4. 多人合作时，可以配置 `mfsu.development.output` 配置预编译依赖输出目录并添加到 git 中，在其他开发者启动时，就可以免去再次编译依赖的过程。

#### 特性

- 预编译：默认情况下，预编译将会将依赖构建到 `~/.umi/.cache/.mfsu` 下。并且使用了 webpack 缓存，减少再次编译依赖的时间。
- diff：预编译时，会将本次的依赖信息构建到 `~/.mfsu/MFSU_CACHE.json` 中，用于依赖的 diff。
- 持久化缓存：对于预编译依赖的请求，开启了`cache-control: max-age=31536000,immutable`，减少浏览器刷新拉取依赖的时间。

### 构建阶段

> warning: 由于预编译依赖实现了部分的 tree-shaking，不建议在打包大小敏感的项目中启用生产模式。

1. 配置 config.ts：`mfsu.production = {}`以开启生产模式。
2. 执行命令：`umi build`，默认情况下将会将生产依赖预编译到 `~/.mfsu-production` 中。
3. umi 会将依赖外的产物构建到 `~/dist` 中，mfsu 再将生产预编译依赖移动到输出目录中。
4. 使用 mfsu 生产模式，可以将 `~/.mfsu-production` 添加到 git 中。在部署时，仅编译应用文件，速度快到飞起。

和creat-react-app的不同

create-react-app 是基于 webpack 的打包层方案，包含 build、dev、lint 等，他在打包层把体验做到了极致，但是不包含路由，不是框架，也不支持配置。所以，如果大家想基于他修改部分配置，或者希望在打包层之外也做技术收敛时，就会遇到困难。

和nextjs的不同

next.js 是个很好的选择，Umi 很多功能是参考 next.js 做的。要说有哪些地方不如 Umi，我觉得可能是不够贴近业务，不够接地气。比如 antd、dva 的深度整合，比如国际化、权限、数据流、配置式路由、补丁方案、自动化 external 方面等等一线开发者才会遇到的问题。

### 约定式路由

除配置式路由外，Umi 也支持约定式路由。约定式路由也叫文件路由，就是不需要手写配置，文件系统即路由，通过目录和文件及其命名分析出路由配置。

**如果没有 routes 配置，Umi 会进入约定式路由模式**，然后分析 `src/pages` 目录拿到路由配置。

动态路由

约定 `[]` 包裹的文件或文件夹为动态路由。

嵌套路由

Umi 里约定目录下有 `_layout.tsx` 时会生成嵌套路由，以 `_layout.tsx` 为该目录的 layout。layout 文件需要返回一个 React 组件，并通过 `props.children` 渲染子组件。

404路由

约定 `src/pages/404.tsx` 为 404 页面，需返回 React 组件。

权限路由

通过指定高阶组件 `wrappers` 达成效果。

### 页面跳转

在 umi 里，页面之间跳转有两种方式：声明式和命令式。

声明式

通过Link使用，通常作为react 组件使用

```react
import { Link } from 'umi';

export default () => (
  <Link to="/list">Go to list page</Link>
);
```

命令式

通过history使用，在事件处理中调用

```react
import { history } from 'umi';

function goToListPage() {
  history.push('/list');
}
```

### config

proxy

配置http-proxy-middleware的proxy

```javascript
proxy: {
  '/api': {
    'target': 'http://jsonplaceholder.typicode.com/',
    'changeOrigin': true,
    'pathRewrite': { '^/api' : '' },
  }
}
```

publicPath

配置 webpack 的 publicPath。当打包的时候，webpack 会在静态文件路径前面添加 `publicPath` 的值，当你需要修改静态文件地址时，比如使用 CDN 部署，把 `publicPath` 的值设为 CDN 的值就可以。如果使用一些特殊的文件系统，比如混合开发或者 cordova 等技术，可以尝试将 `publicPath` 设置成 `./` 相对路径

相对路径 `./` 有一些限制，例如不支持多层路由 `/foo/bar`，只支持单层路径 `/foo`

如果你的应用部署在域名的子路径上，例如 `https://www.your-app.com/foo/`，你需要设置 `publicPath` 为 `/foo/`，如果同时要兼顾开发环境正常调试，你可以这样配置

```javascript
import { defineConfig } from 'umi';

export default defineConfig({
  publicPath: process.env.NODE_ENV === 'production' ? '/foo/' : '/',
});
```



### API

useIntl

umi的useIntl是基于react-intl的。使用formatMessage api

useRequest

Prompt

提供一个用户离开页面时的提示选择

```react
import { Prompt } from 'umi';

export default () => {
  return (
    <div>
      {/* 用户离开页面时提示一个选择 */}
      <Prompt message="你确定要离开么？" />

      {/* 用户要跳转到首页时，提示一个选择 */}
      <Prompt
        message={(location) => {
          return location.pathname !== '/' ? true : `您确定要跳转到首页么？`;
        }}
      />

      {/* 根据一个状态来确定用户离开页面时是否给一个提示选择 */}
      <Prompt when={formIsHalfFilledOut} message="您确定半途而废么？" />
    </div>
  );
};
```

有时候这个提示会连续出现两次，跳转方法一次push一次replace，可以利用return为true取消第二次提示

### msfu原理

mfsu，是 Module Federation Speed Up 的缩写，含义为：基于 webpack5 的 module federation 特性的提速方案。它有以下的特点：

- 快！项目启动只需要3s，热更新提速 50%，生产模式部署提升 50 倍 1 ！
- 全！基于 webpack 的研发体系，生态更加完善！未来也会加入 esbuild，让快更快！
- 稳！蚂蚁内部近千个前端项目，都将开启 mfsu！
- 狠！云谦老师主持开发，解决 bug 就是快刀斩乱麻！

module federation 是 webpack5 提出的新特性，含义为模块联邦。主要是使用于微前端场景。联邦的含义是：我可以通过一个个分散的联邦，组合成一个强大的帝国。所以在 webpack 的模块联邦里，每一个应用可以对外暴露自己的一些组件，供其他应用使用

既然模块联邦可以让一个应用从另一个应用拉取模块，我们可以不可以构建一个包含了所有依赖的应用呢

在每一次启动项目和热更新的时候，webpack 都需要对依赖和项目文件进行编译，尤其我们项目中的大部分质量都存在于 node_modules。那么如果我们减少了对依赖的重新编译，是不是可以减少项目启动和热更新的时间呢？

基于这样的思考，mfsu 的方案已经呼之欲出：我们可以利用 webpack5 的 module federation 特性，构建一个虚拟的 federation 应用，随后，我们的项目直接仅使用编译好的依赖，这样就可以直接减去热更新和启动时对依赖的重新编译

因此，mfsu 快的原因很简单，就是直接砍掉了对依赖的编译过程！

提前编译了依赖，带来了很多的好处：

1. 即使项目规模继续增大，依赖的数量继续增多，启动和热更新都可以保持性能！
2. 可以将预编译产物在团队中进行同步，其他同学可以直接享受到预编译带来的快乐！
3. 生产模式下，可以持续使用预编译好的依赖，以加快部署速度！



## redwoodjs

基于GraphQL、prisma、fastify和react的全栈开发框架

form

```react
import { MetaTags } from '@redwoodjs/web'
import {
  Form,
  TextField,
  TextAreaField,
  Submit,
  SubmitHandler
} from '@redwoodjs/forms'

interface FormValues {
  name: string
  email: string
  message: string
}

const ContactPage = () => {
  const onSubmit: SubmitHandler<FormValues> = (data) => {
    console.log(data)
  }

  return (
    <>
      <MetaTags title="Contact" description="Contact page" />

      <Form onSubmit={onSubmit}>
        <label htmlFor="name">Name</label>
        <TextField name="name" />

        <label htmlFor="email">Email</label>
        <TextField name="email" />

        <label htmlFor="message">Message</label>
        <TextAreaField name="message" />

        <Submit>Save</Submit>
      </Form>
    </>
  )
}

export default ContactPage
```

app配置

使用redwool.toml文件进行配置



## refine

**refine 是一个基于 React 的快速框架✨网络应用程序的开发。它消除了** CRUD 操作所需的重复性任务，并为身份验证 **、**访问控制**、**路由**、**网络**、**状态管理**和 **i18n** 等关键部分提供了行业标准解决方案。

refine 在设计上是*无限制的*，因此提供了无限的样式和定制选项

⚙️使用**单个 CLI 命令**进行零配置、**一分钟设置**

🔌用于 **15 多种后端服务** 的连接器，包括 [REST API](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fsimple-rest)、[GraphQL](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fgraphql)、[NestJs CRUD](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fnestjsx-crud)、[Airtable](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fairtable)、[Strapi](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fstrapi)、[Strapi v4](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fstrapi-v4)、[Strapi GraphQL](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fstrapi-graphql)、[Supabase](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fsupabase)、[Hasura](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fhasura)、[Nhost](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fnhost)、[Appwrite](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Fappwrite)、[Firebase](https://link.juejin.cn?target=https%3A%2F%2Ffirebase.google.com%2F)、[Directus](https://link.juejin.cn?target=https%3A%2F%2Fdirectus.io%2F) 和 [Altogic](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frefinedev%2Frefine%2Ftree%2Fmaster%2Fpackages%2Faltogic)

🌐 Next.js 或 **Remix \**\*\*支持\*\**\* SSR**

⚛使用 **React Query** 完善 **状态管理**和**突变**

🔀 使用您选择的任何路由器库的**高级路由**

🔐**无缝身份验证**和**访问控制**流程的提供商

⚡**对实时 / 实时应用程序**的开箱即用支持

📄轻松的**审核日志**和**文档版本控制**

💬支持任何 **i18n** 框架

💪面向未来的**稳健架构**

✅完整的**测试覆盖**

创建/运行项目

```shell
npx superplate-cli --preset refine-antd my-project

npm run dev
```

新建页面

```tsx
import { Refine, useMany } from "@pankod/refine-core";
import {
    useTable,
    List,
    Table,
    DateField,
    Layout,
    ReadyPage,
    notificationProvider,
    ErrorComponent,
} from "@pankod/refine-antd";
import routerProvider from "@pankod/refine-react-router-v6";
import dataProvider from "@pankod/refine-simple-rest";

import "@pankod/refine-antd/dist/styles.min.css";

const App: React.FC = () => {
    return (
        <Refine
            routerProvider={routerProvider}
            dataProvider={dataProvider("https://api.fake-rest.refine.dev")}
            resources={[{ name: "posts", list: PostList }]}
            Layout={Layout}
            ReadyPage={ReadyPage}
            notificationProvider={notificationProvider}
            catchAll={<ErrorComponent />}
        />
    );
};

export const PostList: React.FC = () => {
    const { tableProps } = useTable<IPost>();

    const categoryIds =
        tableProps?.dataSource?.map((item) => item.category.id) ?? [];

    const { data, isLoading } = useMany<ICategory>({
        resource: "categories",
        ids: categoryIds,
        queryOptions: {
            enabled: categoryIds.length > 0,
        },
    });

    return (
        <List>
            <Table<IPost> {...tableProps} rowKey="id">
                <Table.Column dataIndex="title" title="title" />
                <Table.Column
                    dataIndex={["category", "id"]}
                    title="category"
                    render={(value: number) => {
                        if (isLoading) {
                            return "loading...";
                        }

                        return data?.data.find(
                            (item: ICategory) => item.id === value,
                        )?.title;
                    }}
                />
                <Table.Column
                    dataIndex="createdAt"
                    title="createdAt"
                    render={(value) => <DateField format="LLL" value={value} />}
                />
            </Table>
        </List>
    );
};

export default App;

interface IPost {
  title: string;
  createdAt: string;
  category: { id: number };
}

interface ICategory {
  id: number;
  title: string;
}
```



## React18

### 并发模式

useTransition是React中用于挂起的hook

```react
const [startTransition, isPending] = useTransition({ timeoutMs: 3000 });

<button disabled={isPending}
  startTransition(()=>{
   	<fetch Calls 
  })>
</button>
{isPending? "Loading": null}
```



### 为获取数据的Suspense

Suspense使组件能够在渲染之前等待一段预定的时间



## React XSS

前端一般会面临 XSS 这样的安全风险，但随着 React 等现代前端框架的流行，使我们在平时开发时不用太关注安全问题。以 React 为例，React 从设计层面上就具备了很好的防御 XSS 的能力。

XSS无论使用哪种攻击方式，其本质就是将恶意代码注入到应用中，浏览器去默认执行。React 官方中提到了 React DOM 在渲染所有输入内容之前，默认会进行转义。它可以确保在你的应用中，永远不会注入那些并非自己明确编写的内容。所有的内容在渲染之前都被转换成了字符串，因此恶意代码无法成功注入，从而有效地防止了 XSS 攻击。

自动转义

React 在渲染 HTML 内容和渲染 DOM 属性时都会将 `"'&<>` 这几个字符进行转义，转义部分源码如下

```javascript
for (index = match.index; index < str.length; index++) {
    switch (str.charCodeAt(index)) {
      case 34: // "
        escape = '&quot;';
        break;
      case 38: // &
        escape = '&amp;';
        break;
      case 39: // '
        escape = '&#x27;';
        break;
      case 60: // <
        escape = '&lt;';
        break;
      case 62: // >
        escape = '&gt;';
        break;
      default:
        continue;
    }
  }
```

这段代码是 React 在渲染到浏览器前进行的转义，可以看到对浏览器有特殊含义的字符都被转义了，恶意代码在渲染到 HTML 前都被转成了字符串，如下

```html
// 一段恶意代码
<img src="empty.png" onerror ="alert('xss')"> 
// 转义后输出到 html 中
&lt;img src=&quot;empty.png&quot; onerror =&quot;alert(&#x27;xss&#x27;)&quot;&gt; 
```

### 可能引起漏洞的写法

使用dangerouslySetInnerHTML

`dangerouslySetInnerHTML` 是 React 为浏览器 DOM 提供 `innerHTML` 的替换方案。通常来讲，使用代码直接设置 HTML 存在风险，因为很容易使用户暴露在 XSS 攻击下，因为当使用 `dangerouslySetInnerHTML` 时，React 将不会对输入进行任何处理并直接渲染到 HTML 中，如果攻击者在 dangerouslySetInnerHTML 传入了恶意代码，那么浏览器将会运行恶意代码。

```javascript
function getNonChildrenInnerMarkup(props) {
  const innerHTML = props.dangerouslySetInnerHTML; // 有dangerouslySetInnerHTML属性，会不经转义就渲染__html的内容
  if (innerHTML != null) {
    if (innerHTML.__html != null) {
      return innerHTML.__html;
    }
  } else {
    const content = props.children;
    if (typeof content === 'string' || typeof content === 'number') {
      return escapeTextForBrowser(content);
    }
  }
  return null;
}
```

所以平时开发时最好避免使用 `dangerouslySetInnerHTML`，如果不得不使用的话，前端或服务端必须对输入进行相关验证，例如对特殊输入进行过滤、转义等处理。前端这边处理的话，推荐使用[白名单过滤](https://link.juejin.cn?target=https%3A%2F%2Fjsxss.com%2Fzh%2Findex.html)，通过白名单控制允许的 HTML 标签及各标签的属性

通过用户提供的对象来创建react组件

```react
// 用户的输入
const userProvidePropsString = `{"dangerouslySetInnerHTML":{"__html":"<img onerror='alert(\"xss\");' src='empty.png' />"}}"`;
// 经过 JSON 转换
const userProvideProps = JSON.parse(userProvidePropsString);
// userProvideProps = {
//   dangerouslySetInnerHTML: {
//     "__html": `<img onerror='alert("xss");' src='empty.png' />`
//      }
// };
render() {
     // 出于某种原因解析用户提供的 JSON 并将对象作为 props 传递
    return <div {...userProvideProps} /> 
}
```

这段代码将用户提供的数据进行 JSON 转换后直接当做 `div` 的属性，当用户构造了类似例子中的特殊字符串时，页面就会被注入恶意代码，所以要注意平时在开发中不要直接使用用户的输入作为属性。

使用用户输入的值来渲染 a 标签的 href 属性，或类似 img 标签的 src 属性等

```javascript
const userWebsite = "javascript:alert('xss');";
<a href={userWebsite}></a>
```

如果没有对该 URL 进行过滤以防止通过 `javascript:` 或 `data:` 来执行 JavaScript，则攻击者可以构造 XSS 攻击，此处会有潜在的安全问题。 用户提供的 URL 需要在前端或者服务端在入库之前进行验证并过滤。

服务端如何防止XSS攻击

服务端作为最后一道防线，也需要做一些措施以防止 XSS 攻击，一般涉及以下几方面：

- 在接收到用户输入时，需要对输入进行尽可能严格的过滤，过滤或移除特殊的 HTML 标签、JS 事件的关键字等。
- 在输出时对数据进行转义，根据输出语境 (html/javascript/css/url)，进行对应的转义
- 对关键 Cookie 设置 http-only 属性，JS 脚本就不能访问到 http-only 的 Cookie 了
- 利用 [CSP](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FHTTP%2FCSP) 来抵御或者削弱 XSS 攻击，一个 CSP 兼容的浏览器将会仅执行从白名单域获取到的脚本文件，忽略所有的其他脚本 (包括内联脚本和 HTML 的事件处理属性)

出现 XSS 漏洞本质上是输入输出验证不充分，React 在设计上已经很安全了，但是一些反模式的写法还是会引起安全漏洞。Vue 也是类似，Vue 做的安全措施主要也是转义，HTML 的内容和动态绑定的属性都会进行转义。无论使用 React 或 Vue 等前端框架，都不能百分百的防止 XSS 攻击，所以服务端必须对前端参数做一些验证，包括但不限于特殊字符转义、标签、属性白名单过滤等。一旦出现安全问题一般都是挺严重的，不管是敏感数据被窃取或者用户资金被盗，损失往往无法挽回。我们平时开发中需要保持安全意识，保持代码的可靠性和安全性。

