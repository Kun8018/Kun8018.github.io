---
title: React
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: http://cdn.kunkunzhang.top/react.png
---

​      前端框架，快速开发页面，函数式编程，与后端api快速搭建

<!--more-->

## JSX语法

　JSX是React中为了便于编写而创建的语法，

jsx也就是js中写xml的意思

组件

函数  function 



参数传递

外部`...perpson`表示接收外部数组内的所有东西，称为属性扩散

构造函数使用组件外部参数时，必须使用在构造函数的参数列表中定义props属性接收，props是形参，只能读取，不能更改外部数据

类

使用类时默认类中默认有constructor构造器

类继承

关键字 extends 

继承类中的constructor必须在第一行有super(),表示对父类中的constructor 的引用

在constructor中引用外部数据也必须用形参



  ```react
render (){
   return {
       
   }
}
  ```

render中直接用this.props使用外界传递的参数



导出函数组件和类组件

export default class 

类组件是有状态组件，有自己的state和生命周期函数，函数组件是无状态组件

### jsx return与createElement api

jsx元素就是调用react.createElement的语法糖。一般来说，使用了jsx就不需要使用createElement和createFactory这两个api



#### jsx/runtime

https://zhuanlan.zhihu.com/p/626110313

`react/jsx-runtime` 是一个 [React](https://www.google.com/search?sca_esv=044a3262e10bcb9e&cs=0&sxsrf=AE3TifOVMgd6qcRSCS5QQbW--QrfL-qMhQ%3A1758702340915&q=React&sa=X&ved=2ahUKEwjk4JLi_PCPAxX-Y_UHHe5uEHkQxccNegQIAhAB&mstk=AUtExfCU1u97l9CkmdViIhlgLctn9TJv_IJNcloUn0oZRpsIZDX8UiTPLwps002Guf3nI248r_wNGHZLdZY_7aa6wT-sOPr00ctb0Sb0EMEBGCTkI3PAniBF6VQ4AQYS2297xdQPySDkFm1FjUR-8sq_5K_ukOYW4e43dg6ja_EKQvPvz57ibY6SUkqe22_qACLCSrRVynFChZnVgDDiSGMbGUDxEpb2_PLXPUT92SvYVZD1wBotbXVXqQyFfRQ4JvR3VPvxxeKM5YhZ6NquBaH91zpn&csui=3) 提供的包，用于在React v17 及更高版本中实现JSX 转换为React 元素的运行时功能。它的出现是为了提供一种新的、无需在每个作用域中显式导入JSX 转换的运行时函数的方式

主要作用：

- **提供运行时函数：**

  该包提供了运行时所需的函数，以便将JSX 语法转换为React 元素。

- **简化JSX 转换：**

  在React v17 版本中引入，它允许开发者在不导入 `React` 对象的情况下使用JSX，并会自动处理JSX 转换。

为什么使用它：

- **避免重复导入：**

  它解决了在每个文件开头都需要导入 `React` 才能使用JSX 的问题，从而简化了代码。

- **更高效的编译过程：**

  通过在运行时提供转换函数，它优化了JSX 的编译和执行流程，提升了开发体验。

## 创建React项目

安装包

```shell
npm i react-router-dom --save
```

创建react项目

```shell
npm install -g create-react-app
```

使用create-react-app命令创建项目

```shell
create-react-app [项目名]
```

启动项目

```shell
npm start 
```

### 为什么会有react和react-dom两个包？什么时候替换react-dom

react在v0.14之前是没有React DOM的，所有的功能都包含在React里。从v0.14开始，react被拆分成react和reactDom，因为有了react Native，react只负责web和mobile的通用核心部分，负责DOM操作的分到ReactDOM中，负责Mobile的包含在react-native中

react-dom只做和浏览器或者DOM相关的操作。如ReactDOM.render,reactDom.findDomNode(),如果是服务端渲染可以reactDOM.renderToString()。React不仅能通过ReactDom和web页面打交道，还能在服务端SSR，移动端React Native和桌面端Electron

web端代码

```react
import React from 'react'
import ReactDOM from 'react-dom'

const App = () => (
	<div>
  	<h1>Hello React</h1>
  </div>
)

ReactDOM.render(<App/>,document.getElementById('root'))
```

React-native

```react
import React from 'react'
import { Text, View} from 'react-native'

const WelcomeScreen = () => (
	<View>
  	<Text>Hello</Text>
  </View>
)
```

### React包api：

创建React组件：React.Component()、React.PureComponent()

创建可包装函数：React.memo()：如果你的组件在相同 props 的情况下渲染相同的结果，那么你可以通过将其包装在 `React.memo` 中调用，以此通过记忆组件渲染结果的方式来提高组件的性能表现。这意味着在这种情况下，React 将跳过渲染组件的操作并直接复用最近一次渲染的结果。

`React.memo` 仅检查 props 变更。如果函数组件被 `React.memo` 包裹，且其实现中拥有 [`useState`](https://zh-hans.reactjs.org/docs/hooks-state.html)，[`useReducer`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usereducer) 或 [`useContext`](https://zh-hans.reactjs.org/docs/hooks-reference.html#usecontext) 的 Hook，当 state 或 context 发生变化时，它仍会重新渲染。

操作元素的api，进行转换元素操作：

`cloneElement() `： 以 `element` 元素为样板克隆并返回新的 React 元素。`config` 中应包含新的 props，`key` 或 `ref`。返回元素的 props 是将新的 props 与原始元素的 props 浅层合并后的结果。新的子元素将取代现有的子元素，如果在 `config` 中未出现 `key` 或 `ref`，那么原始元素的 `key` 和 `ref` 将被保留。

`createElement()`： 创建并返回指定类型的新 [React 元素](https://zh-hans.reactjs.org/docs/rendering-elements.html)。其中的类型参数既可以是标签名字符串（如 `'div'` 或 `'span'`），也可以是 [React 组件](https://zh-hans.reactjs.org/docs/components-and-props.html) 类型 （class 组件或函数组件），或是 [React fragment](https://zh-hans.reactjs.org/docs/react-api.html#reactfragment) 类型。

`isValidElement() `:验证对象是否为 React 元素，返回值为 `true` 或 `false`。

`React.children()`

`createFactory()`: 已废弃，建议使用JSX 或直接调用 `React.createElement()` 来替代它。

Fragments减少不必要嵌套的组件:

​	React.Fragments: `React.Fragment` 组件能够在不额外创建 DOM 元素的情况下，让 `render()` 方法中返回多个元素。

Refs：

React.createRef: `React.createRef` 创建一个能够通过 ref 属性附加到 React 元素的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html)。

React.forwardRef: `React.forwardRef` 会创建一个React组件，这个组件能够将其接受的 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 属性转发到其组件树下的另一个组件中。

Suspense:

​	suspense使组件可以等待某些操作结束后再进行渲染。通过react.lazy动态加载组件

​	react.suspense: `React.Suspense` 可以指定加载指示器（loading indicator），以防其组件树中的某些子组件尚未具备渲染条件。

​	react.lazy: `React.lazy()` 允许你定义一个动态加载的组件。这有助于缩减 bundle 的体积，并延迟加载在初次渲染时未用到的组件。

### React-dom api

- render: ReactDOM.render(element, container[, callback])

  在提供的 `container` 里渲染一个 React 元素，并返回对该组件的[引用](https://zh-hans.reactjs.org/docs/more-about-refs.html)（或者针对[无状态组件](https://zh-hans.reactjs.org/docs/components-and-props.html#function-and-class-components)返回 `null`）。

  如果 React 元素之前已经在 `container` 里渲染过，这将会对其执行更新操作，并仅会在必要时改变 DOM 以映射最新的 React 元素。

  如果提供了可选的回调函数，该回调将在组件被渲染或更新之后被执行。

- hydrate: ReactDOM.hydrate(element, container[, callback])

与 [`render()`](https://zh-hans.reactjs.org/docs/react-dom.html#render) 相同，但它用于在 [`ReactDOMServer`](https://zh-hans.reactjs.org/docs/react-dom-server.html) 渲染的容器中对 HTML 的内容进行 hydrate 操作。React 会尝试在已有标记上绑定事件监听器。

React 希望服务端与客户端渲染的内容完全一致。React 可以弥补文本内容的差异，但是你需要将不匹配的地方作为 bug 进行修复。在开发者模式下，React 会对 hydration 操作过程中的不匹配进行警告。但并不能保证在不匹配的情况下，修补属性的差异。由于性能的关系，这一点非常重要，因为大多是应用中不匹配的情况很少见，并且验证所有标记的成本非常昂贵。

如果单个元素的属性或者文本内容，在服务端和客户端之间有无法避免差异（比如：时间戳），则可以为元素添加 `suppressHydrationWarning={true}` 来消除警告。这种方式只在一级深度上有效，应只作为一种应急方案（escape hatch）。请不要过度使用！除非它是文本内容，否则 React 仍不会尝试修补差异，因此在未来的更新之前，仍会保持不一致。

如果你执意要在服务端与客户端渲染不同内容，你可以采用双重（two-pass）渲染。在客户端渲染不同内容的组件可以读取类似于 `this.state.isClient` 的 state 变量，你可以在 `componentDidMount()` 里将它设置为 `true`。这种方式在初始渲染过程中会与服务端渲染相同的内容，从而避免不匹配的情况出现，但在 hydration 操作之后，会同步进行额外的渲染操作。注意，因为进行了两次渲染，这种方式会使得组件渲染变慢，请小心使用。

记得保证弱网环境下的用户体验。JavaScript 代码的加载要比最初的 HTML 渲染晚的多。因此如果你只在客户端渲染不同的内容，其转换可能会不稳定。但是，如果执行顺利，那么在服务端负责渲染的 shell 会对渲染提供帮助，并且只显示客户端上额外的小组件。

- findDOMNode: ReactDOM.findDOMNode(component)

`findDOMNode` 是一个访问底层 DOM 节点的应急方案（escape hatch）。在大多数情况下，不推荐使用该方法，因为它会破坏组件的抽象结构。

- unmountComponentAtNode: ReactDOM.unmountComponentAtNode(container)

从 DOM 中卸载组件，会将其事件处理器（event handlers）和 state 一并清除。如果指定容器上没有对应已挂载的组件，这个函数什么也不会做。如果组件被移除将会返回 `true`，如果没有组件可被移除将会返回 `false`。

- createPortal: ReactDOM.createPortal(child, container)

创建 portal。通常来讲，当你从组件的 render 方法返回一个元素时，该元素将被挂载到 DOM 节点中离其最近的父节点。 而[Portal](https://zh-hans.reactjs.org/docs/portals.html) 将提供一种将子节点渲染到 DOM 节点中的方式，该节点存在于 DOM 组件的层次结构之外。

一个 portal 的典型用例是当父组件有 `overflow: hidden` 或 `z-index` 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框

portal事件冒泡

尽管 portal 可以被放置在 DOM 树中的任何地方，但在任何其他方面，其行为和普通的 React 子节点行为一致。由于 portal 仍存在于 *React 树*， 且与 *DOM 树* 中的位置无关，那么无论其子节点是否是 portal，像 context 这样的功能特性都是不变的。

这包含事件冒泡。一个从 portal 内部触发的事件会一直冒泡至包含 *React 树*的祖先，即便这些元素并不是 *DOM 树* 中的祖先。

#### 使用portal实现modal

https://120.77.178.201/interview/react-portal.html#%E5%AE%9E%E6%88%98-%E7%94%A8portal%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AAmodal

实现一个`Modal`。为啥要使用`Portal`呢，有如下几个理由：

1. `Modal`组件通常需要覆盖整个页面或页面中的某个部分，因此需要在页面的最上层进行渲染，而不受父组件的限制。
2. `Modal`组件通常是一种对话框形式的 UI 组件，需要与用户进行交互，例如获取用户输入或展示信息等，如果在父组件的上下文中渲染，可能会受到其他组件的干扰或影响交互体验。
3. `Modal`组件通常需要具有固定的位置和尺寸，如果在父组件的上下文中渲染，可能会受到父组件 CSS 样式的影响，导致无法实现固定位置和尺寸的效果。

其他场景：

1. 刚刚提到的`Modal`：一些组件需要渲染在屏幕的中央或者覆盖在其他组件之上，这时候使用`Portal`可以将这些组件渲染到`DOM`树的`body`节点下，从而实现这些效果。
2. 面包屑导航：面包屑导航通常需要在页面的顶部展示，而且需要在组件之间传递信息，使用`Portal`可以在任意位置渲染面包屑导航并传递信息。
3. 多级菜单：多级菜单需要在不同的层级之间渲染，使用`Portal`可以将菜单渲染到body节点下的菜单容器中，从而实现菜单的层级渲染

实现

```typescript
import React, { useCallback, useMemo } from "react";
import Mask from "../Mask"

import ReactDOM from 'react-dom'
import "./index.css"

type IProps = {
    /** modal宽度（默认400） */
    width?: number | string;
    /** 右上角是否显示关闭按钮 */
    closable?: boolean;
    /** 关闭Modal时销毁内部组件实例 */
    destroyOnClose?: boolean;
    /** 显示/隐藏Modal */
    show?: boolean;
    /** 关闭的回调 */
    onCancel?: () => void;
    /** 点击蒙层是否允许关闭 */
    maskClosable?: boolean;
    /** Modal标题 */
    title?: string | React.ReactNode;
    /** Modal内容节点 */
    children?: React.ReactNode;
}

type HeaderProps = {
    /** Modal标题 */
    title?: string | React.ReactNode;
    /** 显示隐藏关闭按钮 */
    showCloseBtn?: boolean,
    /** 关闭的回调 */
    onCancel?: () => void;
}


const Header = ({
    title,
    showCloseBtn,
    onCancel
}: HeaderProps) => {

    const modalHeaderExtraStyle = useMemo(() => ({ justifyContent: showCloseBtn ? 'space-between' : 'left' }), [showCloseBtn])

    return (
        <div className="modal-header" style={modalHeaderExtraStyle}>
            <div className="modal-header-title">{title}</div>
            {
                showCloseBtn && <div className="modal-header-close-btn" onClick={() => onCancel && onCancel()}>x</div>
            }
        </div>
    )
}


export default React.memo<IProps>(({ width = 400, destroyOnClose = false, children, show = true, onCancel, maskClosable = false, title, closable = true }) => {
    /** 点击蒙层关闭的逻辑 */
    const handleMaskClick = useCallback(() => {
        if (!maskClosable) return
        if (onCancel) onCancel()
    }, [
        maskClosable, onCancel
    ])

    const modalContainerExtraStyle = useMemo(() => ({ height: show ? '100vh' : 0 }), [show])

    const modalCardExtraStyle = useMemo(() => ({ width, display: show ? 'block' : 'none' }), [show, width])

    if (!destroyOnClose || show) return ReactDOM.createPortal(
        <div className="modal-container" style={modalContainerExtraStyle}>
            <Mask show={show} onClick={() => handleMaskClick()} />
            <div className="modal-card" style={modalCardExtraStyle} >
                <Header onCancel={() => onCancel && onCancel()} title={title} showCloseBtn={closable} />
                {children}
            </div>
        </div>, document.body
    )

    return <></>
})
```



### React-dom-server

`ReactDOMServer` 对象允许你将组件渲染成静态标记。通常，它被使用在 Node 服务端上，也就是SSR

api

```react
// ES modules
import ReactDOMServer from 'react-dom/server';
// CommonJS
var ReactDOMServer = require('react-dom/server');
```



```javascript
import ReactDOMServer from 'react-dom/server';

ReactDOMServer.renderToString(element)
```

将 React 元素渲染为初始 HTML。React 将返回一个 HTML 字符串。你可以使用此方法在服务端生成 HTML，并在首次请求时将标记下发，以加快页面加载速度，并允许搜索引擎爬取你的页面以达到 SEO 优化的目的。

如果你在已有服务端渲染标记的节点上调用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 方法，React 将会保留该节点且只进行事件处理绑定，从而让你有一个非常高性能的首次加载体验。

```react
ReactDOMServer.renderToStaticMarkup(element)
```

此方法与 [`renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。

如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 [`renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 或在前端上使用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 来代替此方法。

```react
ReactDOMServer.renderToNodeStream(element)
```

将一个 React 元素渲染成其初始 HTML。返回一个可输出 HTML 字符串的[可读流](https://nodejs.org/api/stream.html#stream_readable_streams)。通过可读流输出的 HTML 完全等同于 [`ReactDOMServer.renderToString`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostring) 返回的 HTML。你可以使用本方法在服务器上生成 HTML，并在初始请求时将标记下发，以加快页面加载速度，并允许搜索引擎抓取你的页面以达到 SEO 优化的目的。

如果你在已有服务端渲染标记的节点上调用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 方法，React 将会保留该节点且只进行事件处理绑定，从而让你有一个非常高性能的首次加载体验。

```react
ReactDOMServer.renderToStaticNodeStream(element)
```

此方法与 [`renderToNodeStream`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertonodestream) 相似，但此方法不会在 React 内部创建的额外 DOM 属性，例如 `data-reactroot`。如果你希望把 React 当作静态页面生成器来使用，此方法会非常有用，因为去除额外的属性可以节省一些字节。

通过可读流输出的 HTML，完全等同于 [`ReactDOMServer.renderToStaticMarkup`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertostaticmarkup) 返回的 HTML。

如果你计划在前端使用 React 以使得标记可交互，请不要使用此方法。你可以在服务端上使用 [`renderToNodeStream`](https://zh-hans.reactjs.org/docs/react-dom-server.html#rendertonodestream) 或在前端上使用 [`ReactDOM.hydrate()`](https://zh-hans.reactjs.org/docs/react-dom.html#hydrate) 来代替此方法。

### 严格模式

严格模式启用了以下仅在开发环境下有效的行为：

- 组件将 [额外重新渲染一次](https://zh-hans.react.dev/reference/react/StrictMode#fixing-bugs-found-by-double-rendering-in-development) 以查找由于非纯渲染而引起的错误。
- 组件将 [额外重新运行一次 Effect ](https://zh-hans.react.dev/reference/react/StrictMode#fixing-bugs-found-by-re-running-effects-in-development)以查找由于缺少 Effect 清理而引起的错误。
- 组件将 [额外重新运行一次 refs 回调](https://zh-hans.react.dev/reference/react/StrictMode#fixing-bugs-found-by-re-running-ref-callbacks-in-development) 以查找由于缺少 ref 清理函数而引起的错误。
- 组件将被 [检查是否使用了已弃用的 API](https://zh-hans.react.dev/reference/react/StrictMode#fixing-deprecation-warnings-enabled-by-strict-mode)。

[React 假设编写的每个组件都是纯函数](https://zh-hans.react.dev/learn/keeping-components-pure)。这意味着编写的 React 组件在给定相同的输入（props、state 和 context）时必须始终返回相同的 JSX。

违反此规则的组件会表现得不可预测，并引发错误。为了帮助你找到意外的非纯函数代码，严格模式 **在开发环境中会调用一些函数两次**（仅限应为纯函数的函数）。这些函数包括：

- 组件函数体（仅限顶层逻辑，不包括事件处理程序内的代码）
- 传递给 [`useState`](https://zh-hans.react.dev/reference/react/useState)、[`set` 函数](https://zh-hans.react.dev/reference/react/useState#setstate)、[`useMemo`](https://zh-hans.react.dev/reference/react/useMemo) 或 [`useReducer`](https://zh-hans.react.dev/reference/react/useReducer) 的函数。
- 部分类组件的方法，例如 [`constructor`](https://zh-hans.react.dev/reference/react/Component#constructor)、[`render`](https://zh-hans.react.dev/reference/react/Component#render)、[`shouldComponentUpdate`](https://zh-hans.react.dev/reference/react/Component#shouldcomponentupdate) 等（[请参阅完整列表](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)）。

如果一个函数是纯函数，运行两次不会改变其行为，因为纯函数每次都会产生相同的结果。然而，如果一个函数是非纯函数（例如，它会修改接收到的数据），运行两次通常会产生明显的差异（这就是它是非纯函数的原因！）。这有助于及早发现并修复错误。



## 路由react-router-dom

安装包

```shell
npm i --save react-router-dom
```

react-router与react-router-dom是两个包，前者提供react的核心api，如Router、Route、Switch等，后者包含前者的依赖，并且提供BrowserRoute、Route、Link等api，可以通过dom操作触发事件控制路由。

一般使用后者

导入包,新建Router.js

```jsx
import React from 'react';
import {BrowserRoute as Router,HashRouter,Link} from 'react-router-dom'
//Router组件本身只是一个容器，真正的路由要通过Route组件定义。
//Route用来表示路由规则，有path和component、exact
//Link组件用于取代<a>元素，生成一个链接，允许用户点击后跳转到另一个路由。它基本上就是<a>元素的React 版本，可以接收Router的状态。
import Home from './components/Home'
import About from './components/About'

function router() {
  return (
    <Router>
          <Route path="/home" component={Home}/>
          <Route path="/about" component={About}/>
     </Router>
  );
}
```

Home.jsx

```jsx
import React,{Component} from 'react';
class Home extends Component {
    render() {
        return (
            <div>
            首页
            </div>
       )
    }
}
export default Home
```

About.jsx

```jsx
import React,{Component} from 'react';
class About extends Component {
    render() {
        return (
            <div>
            关于
            </div>
       )
    }
}
export default About
```



```react
<HashRouter>
  <Link to="/home"></Link>
  <Link to="/movie"></Link>
  <Link to="/about"></Link>
  <Route path="/home" compoent={} exact></Route>
  <Route path="/movie/:type/:id" compoent={} exact></Route>
  <Route path="/about" compoent={} exact></Route>
</HashRouter>
```

在app.js中引入

```js
import Router from './Router'

class App extends Component{
    render(){
        return (
           <Router />
        );
    }
}
```

NavLink是一个特殊版本的Link，可以使用activeClassName来设置Link被选中时被附加的class，使用activeStyl

来配置被选中时应用的样式。



### 路由匹配

Switch

```react
import { HashRouter,Route,Switch } from 'react-router-dom'

const BasicRoute = () => (
   <HashRouter>
      <Switch>
         <Route exact path="/" component={Home}/>         
         <Route exact path="/detail" component={Detail}/>
      </Switch>
   </HashRouter>
)
```

### 嵌套路由

router.js

```jsx
import Other from './components/Other'

function router(){
    return{
        <Router>
            <Route path="/home" component={Home} />
            <Route path="/home" render={()=>(
                    <About>
                       <Route path="/about/other" component={other}/>
                    </About>
                )}>
        </Router>
    }
}
```

### 重定向

```jsx
import {Redirect} from "react-router-dom"

return {(
		<Router>
        <Route path="/home" component={Home} />
        <Route path="/" render={
        ()=>(
        <Redirect to="/home"/>)}>
        </Route>
    </Router>
);}
export default router;
```

`IndexRedirect`组件用于访问根路由的时候，将用户重定向到某个子组件。



### 其他API

RouteComponentProps



Prompt

跳转之前提示

```react
/**
 * 使用Prompt的默认提示框阻止跳转
 */
import React, { Component } from 'react';
import { Prompt, Link } from 'react-router-dom';

export default class DefaultPrompt extends Component {
  render() {
    return (
      <div>
        <Link to="/function/message">跳转到function形式的message的页面</Link>
        {/* when为true时表示阻止默认的跳转行为 */}
        <Prompt when={true} message='确认离开此页面？' />
        <h1>
          使用Prompt的默认提示框阻止跳转
        </h1>
      </div>
    )
  }
}
```



withRouter

默认情况下必须是经过路由匹配渲染的组件才存在this.props，才拥有路由参数，才能使用编程式导航的写法，执行this.props.history.push('/detail')跳转到对应路由的页面

然而不是所有组件都直接与路由相连（通过路由跳转到此组件）的，当这些组件需要路由参数时，使用withRouter就可以给此组件传入路由参数，此时就可以使用this.props

设置withRouter很简单只需要两步：（1）引入 （2）将App组件 withRouter() 一下

比如app.js这个组件，一般是首页，不是通过路由跳转过来的，而是直接从浏览器中输入地址打开的，如果不使用withRouter此组件的this.props为空，没法执行props中的history、location、match等方法。

```react
import React,{Component} from 'react'
import {Switch,Route,NavLink,Redirect,withRouter} from 'react-router-dom' //引入withRouter
import One from './One'
import NotFound from './NotFound'
class App extends Component{
    //此时才能获取this.props,包含（history, match, location）三个对象
    console.log(this.props);  //输出{match: {…}, location: {…}, history: {…}, 等}
    render(){return (
        <div className='app'>
            <NavLink to='/one/users'>用户列表</NavLink>
            <NavLink to='/one/companies'>公司列表</NavLink>
            <Switch>
                <Route path='/one/:type?' component={One} />
                <Redirect from='/' to='/one' exact />
                <Route component={NotFound} />
            </Switch>
        </div>)
    }
}
export default withRouter(App);  //这里要执行一下WithRouter
```

match

match是一个匹配路径参数的对象，它有一个属性params，里面的内容就是路径参数，除常用的params属性外，它还有url、path、isExact属性。



history对象

监听路由变化

根据history对象判断路由变化作进一步操作

```react
class Header extends Component {
  
  componentWillReceiveProps (nextProps,nextState){
    if(this.props.location.pathname !== location.pathname){
        
    }
  }
  
  componentDidMount() {
    this.props.history.listen(location => {
      if(this.props.location.pathname !== location.pathname){
        
      }
    })
  }
  
  shouldComponentUpdate (nextProps){
    let preRouteName = this.props.location.pathname;
    let currentRouteName = this.props.location.pathname;
    return preRouteName !== currentRouteName;
  }
}
```

Https://reactrouter.com

### history

react-router中使用的基础包

安装

```shell
npm install history
```

使用

```javascript
import history from 'history';

// Push a new entry onto the history stack.
history.push("/home");

// Push a new entry onto the history stack with a query string
// and some state. Location state does not appear in the URL.
history.push("/home?the=query", { some: "state" });

// If you prefer, use a location-like object to specify the URL.
// This is equivalent to the example above.
history.push(
  {
    pathname: "/home",
    search: "?the=query",
  },
  {
    some: state,
  }
);

// Go back to the previous history entry. The following
// two lines are synonymous.
history.go(-1);
history.back();
```



### nginx与webpack配置

单页应用(SPA)一般只有一个index.html, 导航的跳转都是基于[HTML5 History API](https://link.juejin.cn/?target=http%3A%2F%2Fwww.w3.org%2Fhtml%2Fwg%2Fdrafts%2Fhtml%2Fmaster%2Fsingle-page.html%23the-history-interface)，当用户在越过index.html 页面直接访问这个地址或是通过浏览器的刷新按钮重新获取时，就会出现404问题；

在`webpack.config.js`里面增加配置：

```javascript
devServer {
	historyApiFallback: true
}
```

`webpack` 里面的 `historyApiFallback` 使用的是`connect-history-api-fallback`

```nginx
location /react {
    alias /project/react/;
    # browserHistory模式 404问题
    try_files $uri $uri/ /react/index.html;
    index index.html;
    autoindex on;
}
```

autoindex on; 开启这个，输入到/react 会直接定向到index.html;

try_files 主要解决的是，如果在一些目录下找不到 index.html， 会最终有一个保底资源的路径就是 /react/index.html；



## State与生命周期

与props不同，state是组件内的私有数据，且完全受控于当前组件。

对于上述两种组件，function函数式定义的组件不支持state，所以要使用state需要把组件定义为class式组件

使用时在组件内添加构造函数constuctor

```jsx
class Clock extends React.Component {
    constructor(props){
        super(props)
        this.state = {date: new Date()};
    }
    
    render(){
        return (
            <div>
                <h1>hello</h1>
                <h2>it is {this.state.date.toLocaleTimeString()}.</h2>
            </div>
        )
    }
}
```

修改组件中的state数据时，使用setState

```jsx
this.setState({comment:'hello'});
```

调用setState方法后react会自动更新state

当你需要更新多个state数据时，可以使用setState独立更新，但是如果state之间互相依赖，会涉及到异步更新的问题，此时setState需要接收一个函数而不是简单的对象，

```jsx
this.setState(function(state,props){
    return {
        counter: state.counter + props.increment
    }
})
```

组件可以把state作为props向下传递到子组件中

```jsx
<FormattedData date={this.state.date} />
```

在React中，组件是有状态组件还是无状态组件是由组件的细节决定的，它可能会随着时间的推移而改变。有状态无状态只针对于组件本身，对外相同，因此可以在有状态组件中调用无状态组件，反之亦然。

```react
import React, { Component } from 'react'

export default class LifeCycle extends Component {
    //// props = {age:10,name:'计数器'}
  static defaultProps = {
      name:'计数器'
  }
  constructor(props){
      //Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    super();//this.props = props;
    this.state = {number:0,users:[]};//初始化默认的状态对象
    console.log('1. constructor 初始化 props and state');
  
  }  
  //componentWillMount在渲染过程中可能会执行多次
  componentWillMount(){
    console.log('2. componentWillMount 组件将要挂载');
    //localStorage.get('userss');
  }
  //componentDidMount在渲染过程中永远只有执行一次
  //一般是在componentDidMount执行副作用，进行异步操作
  componentDidMount(){
    console.log('4. componentDidMount 组件挂载完成');
    fetch('https://api.github.com/users').then(res=>res.json()).then(users=>{
        console.log(users);
        this.setState({users});
    });
  }
  shouldComponentUpdate(nextProps,nextState){
    console.log('Counter',nextProps,nextState);
    console.log('5. shouldComponentUpdate 询问组件是否需要更新');
    return true;
  }
  componentWillUpdate(nextProps, nextState){
    console.log('6. componentWillUpdate 组件将要更新');
  }
  componentDidUpdate(prevProps, prevState)){
    console.log('7. componentDidUpdate 组件更新完毕');
  }
  add = ()=>{
      this.setState({number:this.state.number});
  };
  render() {
    console.log('3.render渲染，也就是挂载')
    return (
      <div style={{border:'5px solid red',padding:'5px'}}>
        <p>{this.props.name}:{this.state.number}</p>
        <button onClick={this.add}>+</button>
        <ul>
            {
                this.state.users.map(user=>(<li>{user.login}</li>))
            }
        </ul>
        {this.state.number%2==0&&<SubCounter number={this.state.number}/>}
      </div>
    )
  }
}
class SubCounter extends Component{
    constructor(props){
        super(props);
        this.state = {number:0};
    }
    componentWillUnmount(){
        console.log('SubCounter componentWillUnmount');
    }
    //调用此方法的时候会把新的属性对象和新的状态对象传过来
    shouldComponentUpdate(nextProps,nextState){
        console.log('SubCounter',nextProps,nextState);
        if(nextProps.number%3==0){
            return true;
        }else{
            return false;
        }
    }
    //componentWillReceiveProp 组件收到新的属性对象
    componentWillReceiveProps(){
      console.log('SubCounter 1.componentWillReceiveProps')
    }
    render(){
        console.log('SubCounter  2.render')
        return(
            <div style={{border:'5px solid green'}}>
                <p>{this.props.number}</p>
            </div>
        )
    }
}

作者：秋天不落叶
链接：https://juejin.cn/post/6844904021233238024
```



## 生命周期

生命周期函数

旧版生命周期

ComponentWillMount(){}在新版生命周期中被弃用

ComponentDidMount(){}

ComponentWillReceiveProps(Props)在新版生命周期中被弃用

ShouldComponentUpdate(Props){}

ComponentWillUpdate(Props){}在新版生命周期中被弃用

ComponentDidUpdate(Props){}

ComponentWillUnmount(Props){}

当初次加载DOM时，下列生命周期函数会被依次调用：

- **`constructor()`**：初始化 state 或进行方法绑定
- `static getDerivedStateFromProps()`
- **`render()`**
- **`componentDidMount()`**：会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。

DOM更新时，会依次调用生命周期函数：

- `static getDerivedStateFromProps()`：`getDerivedStateFromProps` 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 `null` 则不更新任何内容。
- `shouldComponentUpdate()`：根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。
- **`render()`**
- `getSnapshotBeforeUpdate()`：在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 `componentDidUpdate()`
- **`componentDidUpdate()`** ：会在更新后会被立即调用。首次渲染不会执行此方法。当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。



`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 `componentDidMount()` 中创建的订阅等。

错误处理生命周期函数

新版生命周期

static getDerivedStateFromProps

`static getDerivedStateFromProps(nextProps,prevState)`：接收父组件传递过来的 `props` 和组件之前的状态，返回一个对象来更新 `state` 或者返回 `null` 来表示接收到的 `props` 没有变化，不需要更新 `state`

**该生命周期钩子的作用：** 将父组件传递过来的 `props` **映射** 到子组件的 `state` 上面，这样组件内部就不用再通过 `this.props.xxx` 获取属性值了，统一通过 `this.state.xxx` 获取。映射就相当于拷贝了一份父组件传过来的 `props` ，作为子组件自己的状态。注意：子组件通过 `setState` 更新自身状态时，不会改变父组件的 `props`

配合 `componentDidUpdate`，可以覆盖 `componentWillReceiveProps` 的所有用法

不把上一个props传递过来的原因：

1.在第一次调用getDerivedStateFromProps后，prevprops参数为null，需要在访问prevprops之前添加if-not-null逻辑检查

2.不把以前的props传递给这个函数，可以把之前不需要用的props释放掉，避免内存占用

**该生命周期钩子触发的时机：**

- 在 React 16.3.0 版本中：在组件实例化、接收到新的 `props` 时会被调用
- 在 React 16.4.0 版本中：在组件实例化、接收到新的 `props` 、组件状态更新时会被调用

getSnapshotBeforeUpdate

`getSnapshotBeforeUpdate(prevProps, prevState)`：**接收父组件传递过来的 `props` 和组件之前的状态，此生命周期钩子必须有返回值，返回值将作为第三个参数传递给** `componentDidUpdate`。必须和 `componentDidUpdate` 一起使用，否则会报错

**该生命周期钩子触发的时机** ：被调用于 `render` 之后、更新 `DOM` 和 `refs` 之前

**该生命周期钩子的作用：** 它能让你在组件更新 `DOM` 和 `refs` 之前，从 `DOM` 中捕获一些信息（例如滚动位置）

配合 `componentDidUpdate`, 可以覆盖 `componentWillUpdate` 的所有用法

### 异步生命周期

react的生命周期函数可以写成async的形式，使用async之后函数体中可以使用await

但是要注意使用，比如

```react
async shouldComponentUpdate() {
  return false;
}
```

正常情况下，如果shouldComponentUpdate返回的是false，组件不会被重新渲染。但是指定为async时返回promise.resolve，一个promise被转化为bool类型，绝对是true，因此shouldComponentUpdate返回的永远是true。

此外，fiber架构中使用异步渲染，render之前的生命周期函数会被调用多次，因此尽量保持render之前的生命周期不要产生副作用

所以async使用的生命周期一般是componentDIdMount和componentDitUpdate



### 旧版生命周期函数被弃用的原因

这三个生命周期容易被和误解和滥用

**ComponentWillReceiveProps(Props)**:react 16之前使用componentWillReceiveProps来做props比较，然后更新组件的state。16及16之后推荐响应props更改的方法使用static getDerivedStateFromProps生命周期。该方法

getDerivedStateFromProps与ComponentDidUpdate配合，可以覆盖ComponentWillReceiveProps的所有用法

**ComponentWillUpdate(Props){}**:官方推荐用getDerivedStateFromProps回调里面处理传递过来的props，然后将异步数据放在componentDidupdate中。

在更新列表数据时，需要保持滚动条的位置。可以在getSnapshotBeforeUpdate获取dom的属性，例如offsetHeight、scrollHeight等，将React的值作为参数传递给componentDidUpdate

getSnapshotBeforeUpdate与ComponentWillUpdate配合，可以覆盖ComponentWillUpdate的所有用法

**ComponentWillMount(){}**:通常为了提前setState，防止二次渲染(第一次是空state渲染，第二次外部数据渲染)，经常在ComponentWillMount请求数据。但是这样可能会导致异步获取外部数据不一定在渲染之前返回，也意味着组件也有可能会被渲染一次，为了后面新版本实现异步渲染，建议请求放在componentDidMount来调用。还有一个问题是在服务端渲染（Next.js）时，componentWillMount会导致服务端和客户端各渲染一次，而componentDidMount只在客户端渲染一次（有问题直接下掉）

### 新旧生命周期兼容

使用unsafe字样



使用react-lifecycles-compat

安装

```shell
npm install react-lifecycles-compat --save
```

使用

```react
import {polyfill} from 'react-lifecycles-compat'

class ExampleComponent extend React.Component {
  static getDerivedStateFromProps(nextProps,prevState){
    
  }
  getSnapshotBeforeUpdate(preProps,prevState){
    
  }
}

polyfill(ExampleComponent)

export default ExampleComponent;
```





##  事件处理

React事件的命名采用小驼峰式命名，而不是纯小写

使用jsx语法是传入函数作为事件处理函数而不是字符串，这体现在写法上

```jsx
//html
<button onclick="active()">
      active
</button>
//React
<button onClick={active}>
      active
</button>
```

此外，阻止标签的默认行为需要在函数中显示使用preventDefault。

```jsx
//html中写法，阻止a标签默认打开链接
<a href="#" onclick="console.log('this link is clicked');return false">
   click me
</a>
//react组件中写法，阻止a标签默认打开链接
function Actionlink() {
    function handleClick(e){
        e.preventDefault();
        console.log('this link is clicked');
    }
    return {
        <a href="#" onClick={handleClick}>
        Click me
        </a>
    }
}
```

e是一个合成事件，类似于python函数的this参数

class声明的组件，事件不会自动绑定this，如果事件未写明绑定this，调用函数时this的值是undefined

在constuctor中绑定

```jsx
constructor(props){
    super(props);
    this.handleClick = this.handleClick.bind(this);
}
```

绑定this参数的三种方式

直接使用bind绑定this参数

在构造函数中传参

箭头函数：()=>(){}

事件传递参数

```jsx
<button onClick={(e) => this.deleteRow(id,e)}>DeleteRow</button>
<button onClick={this.deleteRow.bind(this,id)}>DeleteRow</button>
```

通过箭头函数和function.prototype.bind实现



## 条件渲染

React 中的条件渲染和 JavaScript 中的一样，使用 JavaScript 运算符 [`if`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) 或者[条件运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)去创建元素来表现当前的状态，然后让 React 根据它们来更新 UI。

```react
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```



### 三目运算符与&&的区别

使用三目运算符进行计算时，如果组件中有副作用的函数会把两个组件全部计算完后再进行选择，而&&会进行断路，因此不必担心额外的副作用

#### 状态保留

如果三目运算符在同一位置使用同一组件，则组件状态会被保留，如

```react
const Example1 = ({ condition }) => {
  return (
    <div>
      {condition ? 
        <MyComponent /> : 
        <MyComponent />
      }
    </div>
  );
};
```

&& 则不会被保留



## React18

React16:引入 Fiber 架构，提升渲染性能，新增错误边界等功能。

React 17：事件系统重构，新的 JSX 转换机制，优化事件处理。

React 18：并发渲染、自动批处理、服务器组件，性能大幅提升。

React 19：引入 Actions 和表单状态管理 API，自动优化性能，简化开发体验

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

## React 19

React 19 不仅是向前迈进的一步，而且想要改变开发人员在 React 中构建应用的方式。React 19 计划引入的一些最令人兴奋的特性包括：

- **服务端组件**：通过服务端组件，React 19 能够实现更快的页面加载速度和更好的 SEO 效果。这意味着在将页面交付给用户之前，服务器会预先处理组件，从而提升用户体验和搜索引擎可见性。
- **Actions**：React 19 引入了 Actions，这是一个全新的机制，用于简化网页内数据和交互的管理。通过 Actions，开发人员可以更方便地通过表单更新页面信息，减少复杂性并优化用户体验。
- **优化的资源加载**：React 19 在资源加载方面进行了优化，允许在后台加载站点资源，以实现更平滑的页面过渡。这意味着用户可以在浏览当前页面时，提前加载下一页所需的图片和其他文件，从而减少页面切换时的等待时间。
- **文档元数据**：React 19 引入了一个新的 `<DocumentHead`> 组件，用于简化 SEO 管理。通过该组件，开发人员可以更方便地向页面添加标题和元标签，提高搜索引擎优化效果，而无需进行重复的编码工作。
- **Web Components**：React 19 改善了与 Web Components 标准的兼容性，使开发人员能够更轻松地使用 Web Components 构建灵活、兼容的 Web 应用

### React 编译器

React 编译器一项自动优化工具，旨在通过先进的编译技术减少不必要的重新渲染，提高 React 应用的性能。在深入探究 React 编译器的工作原理之前，我们先回顾一下 React 的核心思维模型。

React的核心是一个**声明式**和**基于组件**的心智模型。在前端开发中，声明式编程意味着描述 UI 的期望最终状态，而无需通过 DOM 操作来指定达到该状态的每一步。同时，基于组件的方法将 UI 元素分解为可重用、简洁、自包含的构建块，促进了模块化并简化了维护。

为了有效地识别需要更新的特定 DOM 元素，React使用了一个称为虚拟 DOM 的内存中UI表示。当应用状态发生变化时，React会将虚拟DOM与真实DOM进行比较，识别出所需的最小更改集，并精确地更新真实DOM。

简而言之，React的心智模型是：每当应用状态发生变化时，React就会重新渲染。然而，有时React可能会过于“反应灵敏”，导致不必要的重新渲染，从而降低应用的性能。

React 对应用状态变化的快速响应能力是一把双刃剑。一方面，由于其声明式方法，它简化了前端开发。另一方面，它可能导致 UI 中组件对状态变化的过度重新渲染。

当处理如对象和数组这样的 JavaScript 数据结构时，重新渲染问题尤为常见。问题在于，JavaScript中没有一种计算效率高的方法来比较两个对象或数组是否相等（即具有相同的键和值）

尽管React组件在每次渲染时可能生成内容相同的本地数组，但React无法直接识别出这一点，因此可能会不必要地触发依赖于该数组中值的组件及其嵌套DOM元素的重新渲染，即使 UI 实际上没有变化。这种不受控制的重新渲染会很快导致性能下降，影响用户体验。

为了优化这种情况并减少不必要的重新渲染，React 开发人员可以利用记忆化技术。记忆化允许缓存基于特定输入的计算结果或组件输出，并在输入未变时直接复用这些结果。这种方法能够显著减少组件的重新渲染次数，提高 React 应用的整体性能和效率。

React 18 提供了以下记忆化工具来帮助我们实现这一目标：

- `React.memo()`：一个高阶组件，允许基于props的浅比较来避免组件的重新渲染，只要`props`没有发生变化。
- `useMemo()`：用于在组件重新渲染之间缓存计算的结果。只有当依赖项之一发生变化时，`useMemo()`才会重新计算并返回新的结果。
- `useCallback()`：用于缓存函数的定义，确保在依赖项未变时不会重新创建函数。

通过使用`useMemo()` Hook，可以优化`<AlphabetList>`组件，避免在其依赖的数据（如数组）未发生变化时进行不必要的重新渲染。这种方法能够显著提高组件的性能，确保 UI 的流畅性和响应性

React 的记忆化工具确实在提升性能上起到了关键作用，但它们确实增加了开发者的工作量和代码复杂度，因为它要求开发者不仅描述 UI 的状态，还需显式管理渲染的优化。这在一定程度上违背了 React 强调的声明式编程哲学

React 编译器，亦名React Forget，是一款针对 React 的优化编译器。它目前已在 Instagram 的网页门户中投入生产使用，并计划在首次开源发布前，扩展至 Meta 旗下的其他应用

最初，React 编译器旨在通过自动生成类似于`memo`、`useMemo`和`useCallback`的调用，来强化React的核心编程模型，进而降低重新渲染的开销。随着时间的推移，该项目已从“自动记忆化编译器”演进为更为先进的“自动响应性编译器”

React Forget 的核心目标，是确保 React 应用能够默认拥有合理的响应性。这意味着应用仅在状态值发生实质性变化时才会触发重新渲染。传统的 React 在对象标识改变时会重新渲染组件，而 React Forget 则通过智能判断，仅在对象的语义内容变化时触发重新渲染，同时避免了深度比较带来的性能损耗。从技术实现来看，React 编译器采用了自动记忆化技术。但开发团队认为，响应性框架是理解其工作原理的更全面视角。

尽管 JavaScript 的动态特性和宽松规则使其优化变得复杂，但 React 编译器通过模拟JavaScript和React的规则，确保了代码编译的安全性和效率。这些规则在限制开发人员操作的同时，也为编译器执行优化提供了安全的操作空间

React 编译器的引入带来了显著的益处：

- **简化记忆化管理**：开发者无需手动编写和维护复杂的记忆化策略，从而降低了代码的复杂性，减少了出错的风险，并极大简化了开发流程。
- **提升开发者体验**：开发者能够更专注于核心功能的构建，无需分心于繁琐的性能优化工作。不仅提高了生产力，还让他们能更充分地利用React的声明式编程优势。
- **加速React应用性能**：React 编译器智能地决定何时渲染组件，有效减少了不必要的计算和资源消耗。这使得用户界面更加流畅和响应迅速，为用户带来了更好的体验，并显著提升了整体应用的性能

React 设定了一套严格的规范，以确保Web应用的高质量。开发者需遵循这些原则，它们同样是 React 编译器背后的基石。

以下是React的几个核心规则：

- **幂等性组件**：React组件在接收到相同的输入（包括`props`、`state`和`context`）时，应始终产生一致的输出。
- **副作用外部化**：副作用操作（如数据获取、订阅或DOM更新）不应嵌入在组件的渲染流程中。它们应被放置在如`useEffect`等生命周期 Hook 中执行。
- **不可变props与state**：React组件中的`props`和`state`应被视为不可变。直接修改它们可能导致错误和不可预测的行为。
- **Hooks参数与返回值的不变性**：一旦值被传递给 React Hooks，它们应保持不变。Hooks依赖其参数和返回值的稳定性来确保组件行为的一致性和可预测性。
- **不可变JSX值**：在 JSX 渲染后，不应修改其中使用的值。任何必要的修改应在JSX创建之前进行，以确保渲染结果的稳定性。
- **组件函数的使用限制**：React组件应通过JSX使用，而非直接作为普通函数调用。
- **Hooks的正确使用**：React Hooks（如`useState`和`useEffect`）应仅在函数组件内部使用。将它们作为普通值传递可能会导致不符合预期的行为并违反Hooks的使用规则。从常规的JavaScript函数中调用hooks可能会导致错误并违反hooks的规则。
- **只在顶层调用hooks**：React hooks 应该始终在函数组件的顶层调用，即在任何条件语句或循环之前。这确保了hooks在每次渲染时都以相同的顺序被调用，并保持其预期的行为。

### 新增hooks

useFormStatus

对于很深层次的 form 组件，逐层传递 props 显然是不合适的，通过 context 或者轻量状态管理库是可以解决的，新增的这个 hook 相当于封装了 context

```javascript
import {useFormStatus} from 'react-dom';

function DesignButton() {
  // useFormStatus 能够读取上层 form 的状态
  const status = useFormStatus();
  return <button type="submit" disabled={status.pending} />
}
```

新增hooks use

```javascript
import {use} from 'react';

function Comments({commentsPromise}) {
  // use 将等待直到 Promise 由 pending 变为 fullfilled/rejected
  const comments = use(commentsPromise);
  return comments.map(comment => <p key={comment.id}>{comment}</p>);
}

function Page({commentsPromise}) {
  // Suspense 将会触发，展示 Loading ...
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Comments commentsPromise={commentsPromise} />
    </Suspense>
  )
}
```

https://juejin.cn/post/7361611938474688562#heading-15

