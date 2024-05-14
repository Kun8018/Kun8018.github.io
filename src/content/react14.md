---
title: React（十四）
date: 2020-04-02 21:40:33
categories: IT
tags:
    - IT，RN，
toc: true
thumbnail: https://s1.ax1x.com/2020/03/27/G9OXNR.png
---

   除了react和vue之外的一些或新或旧的mvvm框架，仅做了解

<!--more-->

## lit

轻量级的前端组件框架

安装

```shell
npm i lit
```

使用

```react
import {LitElement, html, css} from 'lit';
import {customElement, property, state} from 'lit/decorators.js';
/* playground-fold */
import {play, pause, replay} from './icons.js';
/* playground-fold-end */

@customElement("my-timer")
export class MyTimer extends LitElement {
  static styles = css`/* playground-fold */

    :host {
      display: inline-block;
      min-width: 4em;
      text-align: center;
      padding: 0.2em;
      margin: 0.2em 0.1em;
    }
    footer {
      user-select: none;
      font-size: 0.6em;
    }
    /* playground-fold-end */`;

  @property() duration = 60;
  @state() private end: number | null = null;
  @state() private remaining = 0;

  render() {
    const {remaining, running} = this;
    const min = Math.floor(remaining / 60000);
    const sec = pad(min, Math.floor(remaining / 1000 % 60));
    const hun = pad(true, Math.floor(remaining % 1000 / 10));
    return html`
      ${min ? `${min}:${sec}` : `${sec}.${hun}`}
      <footer>
        ${remaining === 0 ? '' : running ?
          html`<span @click=${this.pause}>${pause}</span>` :
          html`<span @click=${this.start}>${play}</span>`}
        <span @click=${this.reset}>${replay}</span>
      </footer>
    `;
  }
  /* playground-fold */

  start() {
    this.end = Date.now() + this.remaining;
    this.tick();
  }

  pause() {
    this.end = null;
  }

  reset() {
    const running = this.running;
    this.remaining = this.duration * 1000;
    this.end = running ? Date.now() + this.remaining : null;
  }

  tick() {
    if (this.running) {
      this.remaining = Math.max(0, this.end! - Date.now());
      requestAnimationFrame(() => this.tick());
    }
  }

  get running() {
    return this.end && this.remaining;
  }

  connectedCallback() {
    super.connectedCallback();
    this.reset();
  }/* playground-fold-end */

}
/* playground-fold */

function pad(pad: unknown, val: number) {
  return pad ? String(val).padStart(2, '0') : val;
}/* playground-fold-end */

```



## Qwikjs

初始化项目

```shell
$ npm init qwik@latest
```

创建和使用组件

```react
import { component$, useStore } from '@builder.io/qwik';

const Counter = component$((props: { step?: number; initial?: number }) => {
  const store = useStore({ count: props.initial || 0 });

  return <button onClick$={() => (store.count += props.step || 1)}>{store.count}</button>;
});

const MyApp = component$(() => {
  return (
    <>
      <div>Single: <Counter /></div>
      <div>Dozens: <Counter step={12}/></div>
    </>
  );
});
```

### Hook

useStyle$()

加载样式

```react
import styles from './code-block.css?inline';
export const CmpStyles = component$(() => {
  useStyles$(styles);
  return <Host>Some text</Host>;
});
```

### Context

qwik有上下文api

```react
import { component$, useStore, useContext, useContextProvider, createContext} from '@builder.io/qwik';

// Create a new context descriptor
export const MyContext = createContext('my-context');

export const Parent = component$(() => {
  // Create some reactive storage
  const state = useStore({
    count: 0,
  });

  // Assign value (state) to the context (MyContext)
  useContextProvider(MyContext, state);
  return (
    <Host>
      <Child />
      <div>Count: {state.count}</div>
    </Host>
  );
});

export const Child = component$(() => {
  // Get reference to state using MyContext
  const state = useContext(MyContext);
  return (
    <Host>
      <button onClick$={() => state.count++}>
        Increment
      </button>
    </Host>
  );
});
```



### 轻组件

除了使用component$命令创建组件，也可以使用传统的function组件创建轻组件

```react
// Lite component: declared using a standard function.
function MyButton(props: { text: string }) {
  return <button>{props.text}</button>;
}

// Component: declared using `component$()`.
const MyApp = component$(() => {
  return (
    <div>
      Some text:
      <MyButton text="Click me" />
    </div>
  );
});
```

轻组件和父组件是一起渲染的

### Slot

类似于react的children

```react
const Collapsible = component$(() => {
  const store = useStore({ isOpen: true });

  return (
    <div class="collapsible">
      <div class="title" onClick$={() => (store.isOpen = !store.isOpen)}>
        <Slot name="title"></Slot>
      </div>
      {store.isOpen ? <Slot /> : null}
    </div>
  );
});

const MyApp = component$(() => {
  return (
    <Collapsible>
      <span q:slot="title">Title text</span>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vivamus vulputate accumsan pretium.
    </Collapsible>
  );
});
```



## alpine.js

前后端在经过彻底的分离之后，服务端渲染再次成为热门议题。除了最主流的`SSR`（服务端渲染+前端水合）方案之外，也出现了适合不同场景的不同方案，例如`JAM`和[TALL](https://link.juejin.cn?target=https%3A%2F%2Ftallstack.dev%2F)。`TALL`是`Laravel`主推的一套快速的全栈开发方案，是`TailwindCSS`、`Alpine.js`、`Laravel`和`Livewire`的首字母缩写。

alpine.js以相比react或vue这些大框架低很多的成本提供了响应式和申明式的组件编写方式

可以像写tailwindcss一样写js

`alpine.js`无需安装，免去了`webpack`、`yarn`之类的学习成本，类似`vue`的语法也非常容易上手。为了保持轻巧，`alpine.js`选择了一些不同的实现方式，例如不依赖虚拟 DOM，模板通过遍历 DOM 来解析等等

引入alpinejs

```html
<script src="https://cdn.jsdelivr.net/gh/alpinejs/alpine@v2.8.0/dist/alpine.js" defer></script>
```

最简单的组件

```html
<div x-data="{ open: false }">
  <button @click="open = true">Open Dropdown</button>
  <ul
      x-show="open"
      @click.away="open = false"
  >
    Dropdown Body
  </ul>
</div>
```

https://juejin.cn/post/6930811299907502093#heading-6

## solidjs

### 响应式

```react
import { createSignal, onCleanup } from "solid-js";
import { render } from "solid-js/web";

const App = () => {
  const [count, setCount] = createSignal(0);
  const timer = setInterval(() => setCount(count() + 1), 1000);
  onCleanup(() => clearInterval(timer));
  return <div>{count()}</div>;
};

render(() => <App />, document.getElementById("app"));
```

SolidJS 中的Signal有以下特点：

- 响应式细粒度更新
- 无需定义 dependencies
- 惰性取值

SolidJS 与 Mobx 和 Vue 的响应式非常相似，但是不会处理 VDOM，而是直接更新 DOM。所以 SolidJS 的性能表现也比较不错

### SolidStart

solidjs的开发框架

`https://github.com/solidjs/solid-start`



## mithril.js





## Bun

bun是基于zig的JavaScript运行时，比Node或者deno具有更快的速度

安装

```shell
$ curl https://bun.sh/install | bash
```

新建一个http.js

```javascript
// http.js
export default {
  port: 3000,
  fetch(request) {
    return new Response("Welcome to Bun!");
  },
};
```

运行

```shell
$ bun run http.js
```

服务端

```javascript
Bun.serve({
  fetch(req) {
    return new Response("HI!");
  },
  error(error: Error) {
    return new Response("Uh oh!!\n" + error.toString(), { status: 500 });
  },
});
```

