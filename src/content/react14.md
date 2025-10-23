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

https://start.solidjs.com/

## servelt

**Svelte**是一个给我同样感觉的JavaScript框架。与React，Vue，Angular和其他框架相比，使用Svelte构建的应用程序是**已编译**，不必为网站访问者提供框架和库代码，因此，所有体验的结果都更加流畅，占用的带宽更少，并且一切感觉更快，更轻便。

与Hugo一样，Hugo会在部署时消失并生成纯HTML，Svelte也会消失，而您所获得的只是纯JavaScript。

Svelte 用于构建快速的 Web 应用。

Svelte 类似 React 和 Vue，都致力让你轻而易举地构建灵活的可交互的用户界面。

与它们截然不同的是：Svelte 在*构建时* 将你的代码转为更优的 JavaScript，而不是在 *运行时* 才解释执行你的代码。

这预示着你无需付出框架本身的性能成本，且首次加载也无额外性能损耗。

你可以使用 Svelte 编写整个应用，也可以用来逐步重构现有代码，整半皆可；还可以只输出单个独立组件（无需强制附带框架自身）并将其用于任意框架中，可谓百面玲珑。

响应式代码

```html
<script>
  let count = 0;
  $: doubled = count * 2;
	
  // 只要更改count 的值，我们就可以在控制台输出它的值
  $: console.log(`the count is ${count}`);

  function handleClick() {
    count += 1;
  }
</script>

<button on:click={handleClick}>
  Clicked {count} {count === 1 ? 'time' : 'times'}
</button>

<p>{count} doubled is {doubled}</p>
```

在 Svelte 中，我们使用 `export` 关键字来实现接受父组件传递过来的属性值。

组件的另一个核心点是 UI，用于描述 UI 的 HTML 是一种“静态”的语言，HTML 是无法表达 *逻辑* 的，比如条件和循环，但 Svelte 可以。Svelte 为其增加了一些逻辑支持，譬如判断、遍历等，以增强你对 UI 的表达能力。

这些额外附加的逻辑能力似曾相识，它十分接近一些以往非常流行的 [Handlebars](https://link.zhihu.com/?target=https%3A//handlebarsjs.com/) 或者 [Mustache](https://link.zhihu.com/?target=https%3A//github.com/janl/mustache.js) 模板语言，例如` { {&#35;if ...} }`，稍有区别的是，Svelte 使用单个大括号括起：`{&#35;if ...}`。

Svelte 的编译器将会编译这些逻辑，通过将 HTML 编译成使用原生 JS 创建的形式，也即这些 HTML 最终将使用 createElement 等原生的 DOM API 来创建。

解释 HTML 模板时，Svelte 就已经明确哪些状态需要更新。

if语句

```html
<script>
  let user = { loggedIn: false };

  function toggle() {
    user.loggedIn = !user.loggedIn;
  }
</script>

{#if user.loggedIn}
  <button on:click={toggle}>Log out</button>
{:else}
  <button on:click={toggle}>Log in</button>
{/if}
```

each语句

```html
<script>
  let cats = [
    { id: 'J---aiyznGQ', name: 'Keyboard Cat' },
    { id: 'z_AbfPXTKms', name: 'Maru' },
    { id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
  ];
</script>

<h1>The Famous Cats of YouTube</h1>

<ul>
  {#each cats as cat}
    <li>
      <a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
        {cat.name}
      </a>
    </li>
  {/each}
</ul>
```

异步数据

```html
<script>
  async function getRandomNumber() {
    const res = await fetch(`https://svelte.dev/tutorial/random-number`);
    const text = await res.text();

    if (res.ok) {
      return text;
    } else {
      throw new Error(text);
    }
  }

  let promise = getRandomNumber();

  function handleClick() {
    promise = getRandomNumber();
  }
</script>

<button on:click={handleClick}>生成随机数字</button>

{#await promise}
  <p>{promise}</p>
{:then number}
  <!-- 返回的结果使用 :then 块连续标记，
       后面提供接收Promise resolve 的变量名称
  -->
  <p>The number is {number}</p>
{:catch error}
  <!-- 如果发生错误，在 :catch 子块中接收错误对象 -->
  <p style="color: red">{error.message}</p>
{/await}

```

### 事件

只执行一次

```html
<script>
  function handleClick() {
    alert('no more alerts')
  }
</script>

<button on:click|once={handleClick}>Click me</button>
```

事件修饰符

- `preventDefault`：在运行事件处理程序之前先调用 `event.preventDefault()` ，这对客户端在表单处理时就很有用。
- `stopPropagation`：调用 `event.stopPropagation()` 停止事件冒泡，防止事件传播到下一个元素。
- `passive`：改进触摸/滚轮事件的滚动性能（Svelte 默认会自动在安全的地方添加它）。
- `nonpassive`：显式设置 `passive: false`。
- `capture`：在 *捕获* 阶段就触发处理程序，而非 *冒泡* 阶段（[参阅 MDN 文档](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events%23Event_bubbling_and_capture)）
- `once`：在运行一次事件处理后，立即将其移除。
- `self`：仅当 event.target 是元素本身时才触发事件处理程序。

事件分发

组件也可以发送（dispath）事件。为此，需要创建一个事件分发器（dispatcher）。

为了展示组件事件，需要先写一个组件

```html
<script>
  import Inner from './Inner.svelte';

  function handleMessage(event) {
    alert(event.detail.text);
  }
</script>

<Inner on:message={handleMessage}/>

<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  function sayHello() {
    dispatch('message', {
      text: 'Hello!'
    });
  }
</script>

<button on:click={sayHello}>Click to say hello</button>
```

this

```svelte

```



### 生命周期

`onMount` 再常见不过，它在组件首次渲染到 DOM 后立即触发

要在销毁组件时运行代码，请使用`onDestroy`。

`beforeUpdate` 函数会在 DOM 更新之前执行，而相应的 `afterUpdate` 则在数据同步到 DOM 之后执行。

`tick` 函数与其他生命周期函数有所不同，不一定是在首次初始化时调用，你可以随时调用它。它返回一个 Promise，并 resolve 任何挂起的状态（如果没有就立即 resolve）。

在 Svelte 中，当你更新组件状态时，它不会立即更新 DOM。相反，它会等到下一个 *微任务* 时更新，期间查看是否需要应用其他的更改，包括其他组件的更改。这样做可以减少一些无用功，让浏览器更有效地批量处理这些事情。



### 状态管理

Svelte 是没有对应的状态库的，因为它内置了状态管理，它被称为 `store`。

当期望脱离组件的层级（父-子）关系且能够在任意位置都能访问某个状态（变量）时，状态管理仍然是非常有用的一个特性。

并非所有的状态都属于在组件层次的结构内。某些时候，有些状态需要被多个毫不相干的组件或普通的 JavaScript 模块访问。

在 Svelte 中，我们通过*store*来实现。

Svelte 将状态划分为两种，一种`可读可写`，一种`只读`，都用`可读可写`（可以读取，也可以修改）虽然省事，不过允许或者说强制状态是只读的，可以防止状态被意外修改。

所谓 store（也即状态），只不过是具有 `subscribe` 方法的对象，它允许当 store 的值改变时自动通知对此感兴趣的相关组件或程序。在 `App.svelte` 中，`count` 便是一个 store，我们在 `count.subscribe` 的回调中设置 `count_value` 的值。

Svelte 假定所有以 `$` 开头的任何标识符都表示引用某个 store 值，而 `$` 实际上是一个保留字符，Svelte 会禁止你使用 `$` 作为你声明的变量的前缀。

```html
<script>
  import { count } from './stores.js';
  import Incrementer from './Incrementer.svelte';
  import Decrementer from './Decrementer.svelte';
  import Reset from './Reset.svelte';
</script>

<h1>count 当前的值是：{$count}</h1>
```

并非所有 store 允许所有人*可写*的。例如，你可能有一个 store 表示鼠标位置或者用户地理位置，允许 ‘外部’ 来修改这个值是没有意义的。对于这种情况，我们可以用*只读*store。

只读状态

并非所有 store 允许所有人*可写*的。例如，你可能有一个 store 表示鼠标位置或者用户地理位置，允许 ‘外部’ 来修改这个值是没有意义的。对于这种情况，我们可以用*只读*store。

```javascript
import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
  const interval = setInterval(() => set(new Date()), 1000);

  return function stop() { clearInterval(interval); };
});
```

`readable` 的第一个参数代表初始值，如果还没有具体的初始值，可以先设为 `null` 或 `undefined`。

第二个参数 `start` 是一个函数，该函数接受一个 `set` 回调用于设置值，并且返回一个 `stop` 函数。

当 store 被第一个订阅者读取时，就会调用 `start` 函数（然后在 `start` 函数中使用 `set` 提供一个最终的值；

最后一个订阅者退订时，调用 `stop` 函数。

状态继承

`derived`来创建一个新的 store，它将继承自某一个或多个*其他*的 store。

```javascript
import { readable, derived } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
  const interval = setInterval(() => {set(new Date()), 1000);

  return function stop() { clearInterval(interval); };
});

const start = new Date();
  
export const elapsed = derived(
  time,
  $time => Math.round(($time - start) / 1000)
);
```

可从多个 store 派生为一个 store，并显式地 `set` 一个值而不是返回它（这对于异步派生的值很有用处）。

自定义store store.js

```javascript
import { writable } from 'svelte/store';

function createCount() {
  const { subscribe, set, update } = writable(0);

  return {
    subscribe,
    increment: () => update(n => n + 1),
    decrement: () => update(n => n - 1),
    reset: () => set(0)
  };
}

export const count = createCount();
```

引入store中的状态

```html
<script>
  import { count } from './stores.js';
</script>

<h1>count 的当前值是：{$count}</h1>

<button on:click={count.increment}>+</button>
<button on:click={count.decrement}>-</button>
<button on:click={count.reset}>reset</button>
```

用RXJS替代store

要获得 `store` 的值，是通过 `store.subscribe` 方法去订阅，store 产生的值会自动推送给订阅者，这点与 RxJS 如出一辙，实际上，你可以将 Svelte 的 `store` 看作是 RxJS 的 `Observable`。

这样做不是没有原因的，其一是为了更容易与类似 rxjs 这样的库相结合，共同发挥作用；其二是认同 RxJS 这套观察者-订阅的理念且值得借鉴。

```html
<script>
  import { fromEvent } from 'rxjs'
  import { map } from 'rxjs/operators'
	
  let position = fromEvent(document, 'mousemove')
	         .pipe(map(e => `鼠标位置: ${e.clientX},${e.clientY}`))
</script>

{$position}
```



### 动画

Svelte 支持两种运动效果，一种是补间动画效果 `tweened`，另一种是弹簧效果 `spring`。这两种效果在页面中都十分常见，因此 Svelte 内置支持它们。

过渡效果更是页面动画的重头戏，由于十分常用，Svelte 为过渡动画效果专门创建了指令级别的支持，分别是 `transition`、`in`、`out`，它们都支持设置附加参数，例如 `duration` 用于设置动画运动的总时长。`transition` 是将进入过渡和退出过渡两者齐集一身

Svelte 无法为各种各样的动画提供预设支持，因此如果某些效果特别引人入胜，你可以通过自定义 CSS 过渡效果来包装它们，有些还需要配合 JS 创建的效果，Svelte 也是支持的。

局部过渡其实是将动画效果限制只应用在元素添加或删除时才触发，而在首次创建时元素不会被执行动画效果。

延迟过渡经常用于例如穿梭框之类的组件中，它可以实现提高用户体验的交互。这种效果通常会有一个`发送端`（send）和`接收端`（receive），例如 `crossfade` 效果。



### 动作

`action` 同样是一种为组件增强能力的特性

`action` 与bind：this不同的是，它有更强的封装性和复用性，将逻辑代码拆分到 JS 文件中供反复应用。

```html
<script>
export function longpress(node, duration) {
  let timer;
	
  const handleMousedown = () => {
    timer = setTimeout(() => {
      node.dispatchEvent(
        new CustomEvent('longpress')
      );
    }, duration * 1000);
  };
	
  const handleMouseup = () => clearTimeout(timer);

  node.addEventListener('mousedown', handleMousedown);
  node.addEventListener('mouseup', handleMouseup);

  return {
    destroy() {
      node.removeEventListener('mousedown', handleMousedown);
      node.removeEventListener('mouseup', handleMouseup);
    }
  };
}
</script>

<button
  use:longpress={duration}
  ...
>按住我别放</button>
```



### 样式



### 插槽

HTML 元素允许相互组合，由此构建复杂的页面。

如果不是普通的 HTML 元素，而是 Svelte 组件，同样支持让组件作为容器，为其添加子组件，以此组合出更大更强的组件，这种容器被称为 `插槽`（Slots）。

组件可以指定`<slot>`元素当内容为空的*缺省*情况应该默认显示什么内容：

我们包含了一个*默认插槽*，用于直接代替组件的子节点。不过有些时候，你需要对放置的内容有更多的控制能力，我们可以使用 *命名插槽* ：

```html
// box.svelte
<div class="box">
  <h3>
    <slot name="name">Unknown name</slot>
  </h3>

  <div>
    <slot name="address">Unknown address</slot>
  </div>
</div>

// app.svelte
<Box>
  <h2>Hello!</h2>
  <p>This is a box. It can contain anything.</p>
  <span slot="name">P. Sherman</span>
  <span slot="address">42 Wallaby Way Sydney</span>
</Box>

<Box />
```

可以编写一个`if`块，块的范围包括了`comments`评论插槽以及插槽所在的整个`<div>`，在`if`块中需要检测`$$slots`中的`comments`插槽是否存在内容：

```html
<script>
  export let title = ''
</script>

<article>
  <h2>{title}</h2>
  <hr />
  <div><slot name="content" /></div>

  {#if $$slots.comments}
  <div style="margin-top: 30px;">
    评论:
    <slot name="comments" />
  </div>
  {/if}

</article>
```



### 上下文

组件 Context（上下文） API 为组件提供一种彼此 '对话' 机制，而无需将数据和函数作为属性来传递，或者发送大量的事件。

Context 相关的 API 函数有两个，分别是`setContext`和`getContext`。

如果组件调用了`setContext(key, context)`，那么其任意的***子组件*** 都可以通过`const context = getContext(key)`来获取这个 context。

Svelte 对比 React 稍微要简单直观一些，抛却了 Provider 和 Consumer 的概念，事实上简单些就很好，就想组件间共享点数据罢了，没必要有板有眼创造一些高级隐喻，让其漂染浓厚的`设计`气息。

模块上下文的主要用途，是提供一种方法，让某个组件的所有实例都共享同一个上下文（这其实用 store 也完全能实现同等功能，只不过模块上下文更显得关联性高一些，以及封装性强一些）。



### 特殊元素

Svelte 提供了各种内置的特殊元素。

`<svelte:self>`，它表示当前组件，允许在某些情况递归自身，这对于展示文件夹树之类的视图很有用，其中文件夹可以包含 *其他* 文件夹。

```html
{#each folders as f}
  <li>
    <svelte:self {...f} />
  </li>
{/each}
```

组件可以通过`<svelte:component>`进行 “变身”，这样可以省掉一系列的`if`块

```html
<script>
  import Red from './Red.svelte';
  import Green from './Green.svelte';
  import Blue from './Blue.svelte';

  let selected = '0';
  $: component = [Red, Green, Blue][selected]
</script>

<select bind:value={selected}>
  <option value="0">红</option>
  <option value="1">绿</option>
  <option value="2">蓝</option>
</select>

<svelte:component this={component} />
```

正如任何 DOM 元素都可以将监听事件一样，你可以通过`<svelte:window>`来监听`window`对象的事件，例如鼠标移动的事件：

```html
<script>
  let x, y
</script>

<svelte:window on:mousemove={ e => ({x, y} = e) } />

<p>鼠标位置：{x}, {y}</p>
```

我们还可以绑定一些`window`的属性，某些业务我们可能需要监测浏览器窗口的大小，原生的写法

是通过监听 `window.onresize` 来实现的。

在 Svelte 中，它更为简单，并且支持绑定

```html
<script>
  let width, height
</script>

<svelte:window bind:innerWidth={width} bind:innerHeight={height} />

<p>Window Size: width={width}, height={height}</p>
```

支持绑定的属性列表如下：

- `innerWidth` - 获得浏览器窗口的内容区域的宽度，包含垂直滚动条(如果有的话)
- `innerHeight` - 获得浏览器窗口的内容区域的高度，包含水平滚动条(如果有的话)
- `outerWidth` - 获得浏览器窗口的宽度
- `outerHeight` - 获得浏览器窗口的高度
- `scrollX` - document 在水平方向已滚动的像素值
- `scrollY` - document 在垂直方向已滚动的像素值
- `online` - `window.navigator.onLine` 的别名，代表当浏览器能够访问网络。

`<svelte:body>`元素允许你监听`document.body`上的事件。这对于`mouseenter`和`mouseleave`事件来说十分有用，它们无法在`window`上触发。

```html
<script>
  let msg = ''
</script>

<svelte:body
  on:mouseenter={() => msg = 'Enter'}
  on:mouseleave={() => msg = 'Leave'}
/>

<div>{msg}</div>
```

`<svelte:head>`元素允许你将其他元素插入到文档中的`<head>`中：

`<head>` 中可以插入例如 `<script>`、`<link>`、`<title>` 和 `<meta>` 等等元素：

```html
<svelte:head>
  <link rel="stylesheet" href="./global.css">
</svelte:head>
```

在服务器端渲染 (SSR) 模式下，`<svelte:head>` 的内容与 HTML 的其余部分是分别返回的。

`<svelte:fragment>`实现类似插槽的功能。

```html
<script>
  import Material from './Material.svelte'
</script>

<Material>
  <svelte:fragment slot="bottom">
    <li>高筋面粉</li>
    <li>低筋面粉</li>
  </svelte:fragment>
</Material>
```

`<svelte:options>`允许你配置编译器的选项。比如希望为组件的变量生成属性访问器（默认不生成）。生成属性访问器的目的，是提供给使用端更容易去访问的，这通常是你编写了一个 Svelte 组件，想同时在 React 或者 Vue 等地方上用时才需要这个选项。

```html
<script>
  export let x = 'hello'
</script>

<svelte:options accessors />

<p>{x}</p>
```

下列是允许在此处设置可选用的值：

- `immutable={true}`：你确信不会使用可变数据，因此编译器可以执行简单的引用相等性检查来确定值是否已更改。
- `immutable={false}`：默认值，Svelte 将按默认的方式处理可变对象的更改
- `accessors={true}`：为组件的 props 添加 getter 和 setter
- `accessors={false}`：默认值
- `namespace="..."`：将使用此组件的命名空间，最常见的就是 `"svg"`
- `tag="..."`：将组件编译为自定义元素时，指定的标签名称

### Svelte Kit

正如 React 之于 [Next](https://nextjs.org/)，Svelte 对应的 Web 框架便是 SvelteKit。

它是一个用 Svelte 构建应用的框架，包括服务器端渲染（SSR）、路由、针对 JS 和 CSS 的代码分割，以及针对不同 Serverless 平台生成不同代码的适配器等等。

### meltui

svelte的headless ui库

https://github.com/melt-ui/melt-ui



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

