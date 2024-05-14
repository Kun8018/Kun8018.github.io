---
title: Vue.js前端框架(四)
date: 2020-10-02 21:40:33
categories: IT
tags:
    - IT，Web,Vue
toc: true
thumbnail: https://cdn.kunkunzhang.top/vue3.png
---

　　vue新版本解析，代号”one-piece“

<!--more-->

## Vue3

## 与vue2相比重大变化

### 全局变量

Vue 2.x 有许多全局 API 和配置，这些 API 和配置可以全局改变 Vue 的行为，比如说创建全局组件、声明全局指令。

虽然这种声明方式很方便，但它也会导致一些问题。从技术上讲，Vue 2 没有“app”的概念，我们定义的应用只是通过 `new Vue()` 创建的根 Vue 实例。从同一个 Vue 构造函数**创建的每个根实例共享相同的全局配置**，

Vue3中新的api

createapp

调用 `createApp` 返回一个应用实例，进行挂载

```javascript
import { createApp } from 'vue'
import MyApp from './MyApp.vue'

const app = createApp(MyApp)
app.mount('#app')
```

其他全局api变化

| 2.x 全局 API               | 3.x 实例 API (`app`)                                         |
| -------------------------- | ------------------------------------------------------------ |
| Vue.config                 | app.config                                                   |
| Vue.config.productionTip   | *removed* ([见下方](https://vue3js.cn/docs/zh/guide/migration/global-api.html#config-productiontip-removed)) |
| Vue.config.ignoredElements | app.config.isCustomElement ([见下方](https://vue3js.cn/docs/zh/guide/migration/global-api.html#config-ignoredelements-is-now-config-iscustomelement)) |
| Vue.component              | app.component                                                |
| Vue.directive              | app.directive                                                |
| Vue.mixin                  | app.mixin                                                    |
| Vue.use                    | app.use ([见下方](https://vue3js.cn/docs/zh/guide/migration/global-api.html#a-note-for-plugin-authors)) |

利用这种方式，可以在应用之间共享全局组件和指令

```javascript
import { createApp } from 'vue'
import Foo from './Foo.vue'
import Bar from './Bar.vue'

const createMyApp = options => {
  const app = createApp(options)
  app.directive('focus' /* ... */)

  return app
}

createMyApp(Foo).mount('#foo')
createMyApp(Bar).mount('#bar')
```

上面代码中，createMyApp(Bar).mount('#bar')Foo 和 Bar 实例及其后代中都可以使用 focus 指令

### 模版语法变化(Fragment片段)

在 2.x 中，不支持多根组件，当用户意外创建多根组件时会发出警告，因此，为了修复此错误，许多组件被包装在一个 `<div>` 中。

在 3.x 中，组件现在可以有多个根节点！但是，这确实要求开发者明确定义属性应该分布在哪里。

```vue
//vue2
<template>
  <div>
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  </div>
</template>
//vue3
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>
  <footer>...</footer>
</template>
```



### 生命周期函数变化

filter将不再被继续允许使用，鼓励使用computed或mothod替代filter功能

其他生命周期函数：

被弃用/替换：

1. beforeCreate -> setup()
2. created -> setup()

重命名的生命周期：

beforeMount -> onBeforeMount

mounted -> onMounted

beforeUpdate -> onBeforeUpdate

updated -> onUpdated

beforeDestroy -> onBeforeUnmount

destroyed -> onUnmounted

errorCaptured -> onErrorCaptured

新的生命周期

onServerPrefetch(): SSR only

注册一个异步函数，在组件实例在服务器上被渲染之前调用。

```vue
<script setup>
import { ref, onServerPrefetch, onMounted } from 'vue'

const data = ref(null)

onServerPrefetch(async () => {
  // 组件作为初始请求的一部分被渲染
  // 在服务器上预抓取数据，因为它比在客户端上更快。
  data.value = await fetchOnServer(/* ... */)
})

onMounted(async () => {
  if (!data.value) {
    // 如果数据在挂载时为空值，这意味着该组件
    // 是在客户端动态渲染的。将转而执行
    // 另一个客户端侧的抓取请求
    data.value = await fetchOnClient(/* ... */)
  }
})
</script>
```



#### setup()



### 组合式API/composition

组合式API是vue中类似于react hook类似的功能，尤雨溪在关于此部分功能的考虑和征求意见稿的全文如下

https://vue3js.cn/vue-composition/

简言之，vue在两种情况下不太友好：

1.随着功能的增长，复杂组件的代码变得越来越难以阅读和理解。这种情况在开发人员阅读他人编写的代码时尤为常见。根本原因是 Vue 现有的 API 迫使我们通过**选项组织代码**，但是有的时候通过**逻辑关系**组织代码更有意义。

目前vue缺少一种简洁且低成本的机制来提取和重用多个组件之间的逻辑。

2.来自大型项目开发者的常见需求是更好的 TypeScript 支持，不支持typescript意味着意味推断不够好。Vue 当前的 API 在集成 TypeScript 时遇到了不小的麻烦，其主要原因是 Vue 依靠一个简单的 `this` 上下文来暴露 property，我们现在使用 `this` 的方式是比较微妙的。

3.缺少一种比较「干净」的在多个组件之间提取和复用逻辑的机制。

Vue 现有的 API 在设计之初没有照顾到类型推导，这使适配 TypeScript 变得复杂。

**composition核心函数：**

1.setup函数

- `setup` 就是将 Vue2.x 中的 `beforeCreate` 和 `created` 代替了，以一个 `setup` 函数的形式，可以灵活组织代码了。
- `setup` 还可以 return 数据或者 template，相当于把 `data` 和 `render` 也一并代替了！

实例：

```vue
<template>
  <div>{{ count }} {{ object.foo }}</div>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const object = reactive({ foo: 'bar' })

    // expose to template
    return {
      count,
      object
    }
  }
}
</script>
```

需要注意的是，setup函数中取消this，取消了 `this`，取而代之的是 `setup` 增加了2个参数：props，组件参数和context，上下文信息

2.Reactive方法：

被 `reactive` 方法包裹后的 `对象` 就变成了一个代理对象，相当于 Vue2.x 中的 `Vue.observable()`。也就可以实现页面和数据之间的双向绑定了。

这个包裹的方法是 `deep` 的， [vue3.md](vue3.md) 对所有嵌套的属性都生效。

3.ref方法

被 `ref` 方法包裹后的 `元素` 就变成了一个代理对象。一般而言，这里的元素参数指 `基本元素` 或者称之为 `inner value`，如：number, string, boolean,null,undefined 等，object 一般不使用 `ref`，而是使用上文的 `reactive`。

也就是说 `ref` 一般适用于某个元素的；而 `reactive` 适用于一个对象。

4.isRef方法

判断一个对象是否 `ref` 代理对象。

```
const unwrapped = isRef(foo) ? foo.value : foo
```

5.toRefs 方法

将一个 `reactive` 代理对象打平，转换为 `ref` 代理对象，使得对象的属性可以直接在 `template` 上使用。

6.computed函数

与 Vue2.x 中的作用类似，获取一个计算结果。当然功能有所增强，不仅支持取值 `get`（默认），还支持赋值 `set`。

结果是一个 `ref` 代理对象，js中取值需要 `.value`。

当 computed 参数使用 object 对象书写时，使用 get 和 set 属性。set 属性可以将这个对象编程一个可写的对象。

也就是说 `computed` 不仅可以获取一个计算结果，它还可以反过来处理 `ref` 或者 `reactive` 对象

7.readonly函数

使用 `readonly` 函数，可以把 `普通 object 对象`、`reactive 对象`、`ref 对象` 返回一个只读对象。

返回的 readonly 对象，一旦修改就会在 console 有 warning 警告。程序还是会照常运行，不会报错。

8.watch函数

watch 函数用来侦听特定的数据源，并在回调函数中执行副作用。默认情况是惰性的，也就是说仅在侦听的源数据变更时才执行回调。

监听不同的数据

```javascript
//侦听reactive的数据
import { defineComponent, ref, reactive, toRefs, watch } from "vue";
export default defineComponent({
  setup() {
    const state = reactive({ nickname: "xiaofan", age: 20 });

    setTimeout(() => {
      state.age++;
    }, 1000);

    // 修改age值时会触发 watch的回调
    watch(
      () => state.age,
      (curAge, preAge) => {
        console.log("新值:", curAge, "老值:", preAge);
      }
    );

    return {
      ...toRefs(state),
    };
  },
});

//侦听ref定义的数据
const year = ref(0);

setTimeout(() => {
  year.value++;
}, 1000);

watch(year, (newVal, oldVal) => {
  console.log("新值:", newVal, "老值:", oldVal);
});


//侦听多个数据时进行合并监听
watch([() => state.age, year], ([curAge, newVal], [preAge, oldVal]) => {
console.log("新值:", curAge, "老值:", preAge); console.log("新值:", newVal,
"老值:", oldVal); });

//侦听复杂数据
const state = reactive({
  room: {
    id: 100,
    attrs: {
      size: "140平方米",
      type: "三室两厅",
    },
  },
});
watch(
  () => state.room,
  (newType, oldType) => {
    console.log("新值:", newType, "老值:", oldType);
  },
  { deep: true }
);
```



停止监听

```javascript
const stopWatchRoom = watch(() => state.room, (newType, oldType) => {
    console.log("新值:", newType, "老值:", oldType);
}, {deep:true});

setTimeout(()=>{
    // 停止监听
    stopWatchRoom()
}, 3000)
```

9.watcheffect函数

watcheffect与watch的区别：

1. watchEffect 不需要手动传入依赖
2. watchEffect 会先执行一次用来自动收集依赖
3. watchEffect 无法获取到变化前的值， 只能获取变化后的值

```javascript
import { reactive, watchEffect } from 'vue'

const state = reactive({
  count: 0,
})

function increment() {
  state.count++
}

const renderContext = {
  state,
  increment,
}

watchEffect(() => {
  // 假设的方法，并不是真实的 API
  renderTemplate(
    `<button @click="increment">{{ state.count }}</button>`,
    renderContext
  )
})
```

#### 自定义hook

约定这些「自定义 Hook」以 use 作为前缀，和普通的函数加以区分

```javascript
import { ref, Ref, computed } from "vue";

type CountResultProps = {
  count: Ref<number>;
  multiple: Ref<number>;
  increase: (delta?: number) => void;
  decrease: (delta?: number) => void;
};

export default function useCount(initValue = 1): CountResultProps {
  const count = ref(initValue);

  const increase = (delta?: number): void => {
    if (typeof delta !== "undefined") {
      count.value += delta;
    } else {
      count.value += 1;
    }
  };
  const multiple = computed(() => count.value * 2);

  const decrease = (delta?: number): void => {
    if (typeof delta !== "undefined") {
      count.value -= delta;
    } else {
      count.value -= 1;
    }
  };

  return {
    count,
    multiple,
    increase,
    decrease,
  };
}

```

使用useCount这个hook

```vue
<template>
  <p>count: {{ count }}</p>
  <p>倍数： {{ multiple }}</p>
  <div>
    <button @click="increase()">加1</button>
    <button @click="decrease()">减一</button>
  </div>
</template>

<script lang="ts">
import useCount from "../hooks/useCount";
 setup() {
    const { count, multiple, increase, decrease } = useCount(10);
        return {
            count,
            multiple,
            increase,
            decrease,
        };
    },
</script>
```

Vue2.x 实现，分散在`data`,`method`,`computed`等， 如果刚接手项目，实在无法快速将`data`字段和`method`关联起来，而 Vue3 的方式可以很明确的看出，将 count 相关的逻辑聚合在一起， 看起来舒服多了， 而且`useCount`还可以扩展更多的功能。 项目开发完之后，后续还会写一篇总结项目中使用到的「自定义 Hooks 的文章」，帮助大家更高效的开发，

### teleport

在子组件`Header`中使用到`Dialog`组件，我们实际开发中经常会在类似的情形下使用到 `Dialog` ，此时`Dialog`就被渲染到一层层子组件内部，处理嵌套组件的定位、`z-index`和样式都变得困难。 `Dialog`从用户感知的层面，应该是一个独立的组件，从 dom 结构应该完全剥离 Vue 顶层组件挂载的 DOM；同时还可以使用到 Vue 组件内的状态（`data`或者`props`）的值。简单来说就是,**即希望继续在组件内部使用`Dialog`, 又希望渲染的 DOM 结构不嵌套在组件的 DOM 中**。 此时就需要 Teleport 上场，我们可以用`<Teleport>`包裹`Dialog`, 此时就建立了一个传送门，可以将`Dialog`渲染的内容传送到任何指定的地方。

### Suspense

在前后端交互获取数据时， 是一个异步过程，一般我们都会提供一个加载中的动画，当数据返回时配合`v-if`来控制数据显示。 如果你使用过`vue-async-manager`这个插件来完成上面的需求， 你对`Suspense`可能不会陌生

Vue3.x 新出的内置组件`Suspense`, 它提供两个`template` slot, 刚开始会渲染一个 fallback 状态下的内容， 直到到达某个条件后才会渲染 default 状态的正式内容， 通过使用`Suspense`组件进行展示异步渲染就更加的简单。:::warning 如果使用 `Suspense`, 要返回一个 promise :::`Suspense` 组件的使用

```vue
  <Suspense>
        <template #default>
            <async-component></async-component>
        </template>
        <template #fallback>
            <div>
                Loading...
            </div>
        </template>
  </Suspense>

<<template>
<div>
    <h4>这个是一个异步加载数据</h4>
    <p>用户名：{{user.nickname}}</p>
    <p>年龄：{{user.age}}</p>
</div>
</template>

<script>
import { defineComponent } from "vue"
import axios from "axios"
export default defineComponent({
    setup(){
        const rawData = await axios.get("http://xxx.xinp.cn/user")
        return {
            user: rawData.data
        }
    }
})
</script>
```



### slot变化

vue2中使用slot插槽

```vue
<!-- 父组件中使用 -->
<template slot="content" slot-scope="scoped">
    <div v-for="item in scoped.data">{{item}}</div>
<template>
  
  // 子组件
<slot name="content" :data="data"></slot>
export default {
    data(){
        return{
            data:["走过来人来人往","不喜欢也得欣赏","陪伴是最长情的告白"]
        }
    }
}
```

Vue3将`slot`和`slot-scope`进行了合并同意使用。 Vue3.0 中`v-slot`：

```vue
<!-- 父组件中使用 -->
 <template v-slot:content="scoped">
   <div v-for="item in scoped.data">{{item}}</div>
</template>

<!-- 也可以简写成： -->
<template #content="{data}">
    <div v-for="item in data">{{item}}</div>
</template>
```

### 异步组件

Vue3 中 使用 `defineAsyncComponent` 定义异步组件，配置选项 `component` 替换为 `loader` ,Loader 函数本身不再接收 resolve 和 reject 参数，且必须返回一个 Promise，

```vue
<template>
  <!-- 异步组件的使用 -->
  <AsyncPage />
</tempate>

<script>
import { defineAsyncComponent } from "vue";

export default {
  components: {
    // 无配置项异步组件
    AsyncPage: defineAsyncComponent(() => import("./NextPage.vue")),

    // 有配置项异步组件
    AsyncPageWithOptions: defineAsyncComponent({
   loader: () => import(".NextPage.vue"),
   delay: 200,
   timeout: 3000,
   errorComponent: () => import("./ErrorComponent.vue"),
   loadingComponent: () => import("./LoadingComponent.vue"),
 })
  },
}
</script>

```

### tree-shaking

Vue3.x 在考虑到 `tree-shaking`的基础上重构了全局和内部 API, 表现结果就是现在的全局 API 需要通过 `ES Module`的引用方式进行具名引用

如vue中使用nextTick()

```javascript
// vue2.x
import Vue from "vue"

Vue.nextTick(()=>{
    ...
})

import { nextTick } from "vue"

nextTick(() =>{
    ...
})
```

类似的api变化

- `Vue.nextTick`
- `Vue.observable`（用 `Vue.reactive` 替换）
- `Vue.version`
- `Vue.compile`（仅限完整版本时可用）
- `Vue.set`（仅在 2.x 兼容版本中可用）
- `Vue.delete`（与上同）

### 新的内置指令

v-pre

跳过该元素及其所有子元素的编译。

元素内具有 `v-pre`，所有 Vue 模板语法都会被保留并按原样渲染。最常见的用例就是显示原始双大括号标签及内容。

v-once

仅渲染元素和组件一次，并跳过之后的更新

```html
<!-- 单个元素 -->
<span v-once>This will never change: {{msg}}</span>
<!-- 带有子元素的元素 -->
<div v-once>
  <h1>comment</h1>
  <p>{{msg}}</p>
</div>
<!-- 组件 -->
<MyComponent v-once :comment="msg" />
<!-- `v-for` 指令 -->
<ul>
  <li v-for="i in list" v-once>{{i}}</li>
</ul>
```

V-memo

缓存一个模板的子树。在元素和组件上都可以使用。为了实现缓存，该指令需要传入一个固定长度的依赖值数组进行比较。如果数组里的每个值都与最后一次的渲染相同，那么整个子树的更新将被跳过

当组件重新渲染，如果 `valueA` 和 `valueB` 都保持不变，这个 `<div>` 及其子项的所有更新都将被跳过。实际上，甚至虚拟 DOM 的 vnode 创建也将被跳过，因为缓存的子树副本可以被重新使用。

正确指定缓存数组很重要，否则应该生效的更新可能被跳过。`v-memo` 传入空依赖数组 (`v-memo="[]"`) 将与 `v-once` 效果相同

`v-memo` 也能被用于在一些默认优化失败的边际情况下，手动避免子组件出现不需要的更新。但是一样的，开发者需要负责指定正确的依赖数组以免跳过必要的更新

v-cloak

用于隐藏尚未完成编译的 DOM 模板。

当使用直接在 DOM 中书写的模板时，可能会出现一种叫做“未编译模板闪现”的情况：用户可能先看到的是还没编译完成的双大括号标签，直到挂载的组件将它们替换为实际渲染的内容。

`v-cloak` 会保留在所绑定的元素上，直到相关组件实例被挂载后才移除。配合像 `[v-cloak] { display: none }` 这样的 CSS 规则，它可以在组件编译完毕前隐藏原始模板。

```vue
<div v-cloak>
  {{ message }}
</div>
[v-cloak] {
  display: none;
}
```



### SFC

Vue 的单文件组件 (即 `*.vue` 文件，英文 Single-File Component，简称 **SFC**) 是一种特殊的文件格式，使我们能够将一个 Vue 组件的模板、逻辑与样式封装在单个文件中。

使用 SFC 必须使用构建工具，但作为回报带来了以下优点：

- 使用熟悉的 HTML、CSS 和 JavaScript 语法编写模块化的组件
- [让本来就强相关的关注点自然内聚](https://cn.vuejs.org/guide/scaling-up/sfc.html#what-about-separation-of-concerns)
- 预编译模板，避免运行时的编译开销
- [组件作用域的 CSS](https://cn.vuejs.org/api/sfc-css-features.html)
- [在使用组合式 API 时语法更简单](https://cn.vuejs.org/api/sfc-script-setup.html)
- 通过交叉分析模板和逻辑代码能进行更多编译时优化
- [更好的 IDE 支持](https://cn.vuejs.org/guide/scaling-up/tooling.html#ide-support)，提供自动补全和对模板中表达式的类型检查
- 开箱即用的模块热更新 (HMR) 支持

SFC 是 Vue 框架提供的一个功能，并且在下列场景中都是官方推荐的项目组织方式：

- 单页面应用 (SPA)
- 静态站点生成 (SSG)
- 任何值得引入构建步骤以获得更好的开发体验 (DX) 的项目

一些有着传统 Web 开发背景的用户可能会因为 SFC 将不同的关注点集合在一处而有所顾虑，觉得 HTML/CSS/JS 应当是分离开的！

要回答这个问题，我们必须对这一点达成共识：**前端开发的关注点不是完全基于文件类型分离的**。前端工程化的最终目的都是为了能够更好地维护代码。关注点分离不应该是教条式地将其视为文件类型的区别和分离，仅仅这样并不够帮我们在日益复杂的前端应用的背景下提高开发效率。

在现代的 UI 开发中，我们发现与其将代码库划分为三个巨大的层，相互交织在一起，不如将它们划分为松散耦合的组件，再按需组合起来。在一个组件中，其模板、逻辑和样式本就是有内在联系的、是耦合的，将它们放在一起，实际上使组件更有内聚性和可维护性。

即使你不喜欢单文件组件这样的形式而仍然选择拆分单独的 JavaScript 和 CSS 文件，也没关系，你还是可以通过[资源导入](https://cn.vuejs.org/api/sfc-spec.html#src-imports)功能获得热更新和预编译等功能的支持。

#### script setup

`<script setup> `是在单文件组件 (SFC) 中使用组合式 API 的编译时语法糖。当同时使用 SFC 与组合式 API 时该语法是默认推荐。相比于普通的 <script> 语法，它具有更多优势：

- 更少的样板内容，更简洁的代码。
- 能够使用纯 TypeScript 声明 props 和自定义事件。
- 更好的运行时性能 (其模板会被编译成同一作用域内的渲染函数，避免了渲染上下文代理对象)。
- 更好的 IDE 类型推导性能 (减少了语言服务器从代码中抽取类型的工作)。

`<script setup> ` 中可以使用顶层 await。结果代码会被编译成 async setup()

#### css功能

当 `<style>` 标签带有 `scoped` attribute 的时候，它的 CSS 只会影响当前组件的元素，和 Shadow DOM 中的样式封装类似。使用时有一些注意事项，不过好处是不需要任何的 polyfill。它的实现方式是通过 PostCSS 进行编译

使用 `scoped` 后，父组件的样式将不会渗透到子组件中。不过，子组件的根节点会同时被父组件的作用域样式和子组件的作用域样式影响。这样设计是为了让父组件可以从布局的角度出发，调整其子组件根元素的样式

处于 `scoped` 样式中的选择器如果想要做更“深度”的选择，也即：影响到子组件，可以使用 `:deep()` 这个伪类

默认情况下，作用域样式不会影响到 `<slot/>` 渲染出来的内容，因为它们被认为是父组件所持有并传递进来的。使用 `:slotted` 伪类以明确地将插槽内容作为选择器的目标

如果想让其中一个样式规则应用到全局，比起另外创建一个 `<style>`，可以使用 `:global` 伪类来实现

一个 `<style module>` 标签会被编译为 [CSS Modules](https://github.com/css-modules/css-modules) 并且将生成的 CSS class 作为 `$style` 对象暴露给组件。也可以通过给 `module` attribute 一个值来自定义注入 class 对象的属性名

```vue
<template>
  <p :class="$style.red">This should be red</p>
</template>

<style module>
.red {
  color: red;
}
</style>

<template>
  <p :class="classes.red">red</p>
</template>

<style module="classes">
.red {
  color: red;
}
</style>
```

和组合式API一起使用

可以通过 `useCssModule` API 在 `setup()` 和 `<script setup>` 中访问注入的 class。对于使用了自定义注入名称的 `<style module>` 块，`useCssModule` 接收一个匹配的 `module` attribute 值作为第一个参数

```javascript
import { useCssModule } from 'vue'

// 在 setup() 作用域中...
// 默认情况下, 返回 <style module> 的 class
useCssModule()

// 具名情况下, 返回 <style module="classes"> 的 class
useCssModule('classes')
```

单文件组件的 `<style>` 标签支持使用 `v-bind` CSS 函数将 CSS 的值链接到动态的组件状态

这个语法同样也适用于 [``](https://cn.vuejs.org/api/sfc-script-setup.html)，且支持 JavaScript 表达式 (需要用引号包裹起来)

```vue
<template>
  <div class="text">hello</div>
</template>

<script>
export default {
  data() {
    return {
      color: 'red'
    }
  }
}
</script>

<style>
.text {
  color: v-bind(color);
}
</style>
```

### vue-demi

跨越vue2和vue3的版本工具

https://github.com/vueuse/vue-demi

https://juejin.cn/post/7032860019880099847/

## Nuxt 3

创建项目

```shell
npx nuxi init nuxt3-app
```

### 数据获取

`Nuxt3`是`SSR`的方案，所以你可能不仅仅只是想要在浏览器端发送请求获取数据，还想在服务器端就获取到数据并渲染组件。

`Nuxt3`提供了 4 种方式使得你可以在服务器端异步获取数据

- useAsyncData
- useLazyAsyncData （useAsyncData+lazy:true）
- useFetch
- useLazyFetch （useFetch+lazy:true）

他们只能在**`setup`**或者是`生命周期钩子`中使用

useAsyncData

项目中可以在`pages`目录、`components`目录和`plugins`目录下使用 useAsyncData 异步获取数据

useLazyAsyncData

这个封装方法等同于是，使用`useAsyncData`方法默认配置了`lazy:true`，执行异步函数时不会阻塞路由的执行。也意味着你必须处理数据为`null`的情况（比如说，在`default`返回的工厂函数中设置一个默认值）。

useFetch

在`pages`目录、`components`目录和`plugins`目录下使用`useFetch`也同样可以获取到任意的 URL 资源。该方法实际上是对 useAsyncData 和$fetch 的封装，提供了一个更便捷的封装方法。（它会根据 URL 和 fetch 参数自动生成一个 key，同时推断出 API 的响应类型）

useLazyFetch

这个封装方法等同于是，使用`useFetch`方法默认配置了`lazy:true`，执行异步函数时不会阻塞路由的执行。也意味着你必须处理数据为`null`的情况（比如说，在`default`返回的工厂函数中设置一个默认值）。

```vue
//例子
<script setup>
const { data } = await useFetch('/api/count')
</script>

<template>
  Page visits: {{ data.count }}
</template>
```

### State

**Nuxt 提供可组合的 `useState` 来创建跨组件的并且对 SSR 友好的响应式状态。**

`useState` 是一个 SSR 友好的 `ref` 替代品。它的值将会在服务端渲染（客户端渲染期间）后保留，并且使用唯一的键在所有组件之间共享。

请不要在`<script setup>` 或 `setup()` 函数以外定义 `const state = ref()` 。
这种 state 将被所有访问您网站的用户共享，并可能导致内存泄漏！

```vue
<script setup>
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>

<template>
  <div>
    Counter: {{ counter }}
    <button @click="counter++">+</button>
    <button @click="counter--">-</button>
  </div>
</template>
```

通过使用[自动导入 composables](https://www.nuxtjs.org.cn/directory-structure/composables.html)，我们可以定义全局的安全类型状态并且在整个应用中导入

```typescript
// composables/states.ts
export const useCounter = () => useState<number>('counter', () => 0)
export const useColor = () => useState<string>('color', () => 'pink')
```

使用状态

```vue
<script setup>
const color = useColor() // Same as useState('color')
</script>

<template>
  <p>Current color: {{ color }}</p>
</template>
```

### 运行时的配置

为了向应用程序的其他部分公开配置和环境变量，您需要在 `nuxt.config` 文件中定义运行时配置，可以使用 [`privateRuntimeConfig` 或 `publicRuntimeConfig` 选项](https://v3.nuxtjs.org/docs/directory-structure/nuxt.config#privateruntimeconfig)

```javascript
export default defineNuxtConfig({
  publicRuntimeConfig: {
    API_BASE: '/api',
  },
  privateRuntimeConfig: {
    API_SECRET: '123', // 仅服务端可用，会覆盖publicRuntimeConfig的配置
  },
})
```

访问运行时配置 Accessing runtime config

```vue
<template>
  <div>
    <div>Token: {{ config.API_AUTH_TOKEN }}</div>
  </div>
</template>

<script setup>
const config = useRuntimeConfig()
</script>
```

在组合函数、组件以及插件中通过 useNuxtApp 访问 nuxtApp 实例。

```javascript
import { useNuxtApp } from '#app'

function useMyComposable() {
  const nuxtApp = useNuxtApp()
  // 获取运行时nuxtApp实例
}
```

## Quasar



## 测试框架

### Vue Test Utils

vue官方提供的测试框架

```javascript
import { mount } from '@vue/test-utils'

// The component to test
const MessageComponent = {
  template: '<p>{{ msg }}</p>',
  props: ['msg']
}

test('displays message', () => {
  const wrapper = mount(MessageComponent, {
    props: {
      msg: 'Hello world'
    }
  })

  // Assert the rendered text of the component
  expect(wrapper.text()).toContain('Hello world')
})
```



### testing-library-vue

Testing-library是react使用较多的测试框架，也支持其他框架，比如vue

如果使用vue2

```shell
npm install --save-dev @testing-library/vue@5
```

如果使用vue3

```shell
npm install --save-dev @testing-library/vue
```

查询函数：https://testing-library.com/docs/queries/about#types-of-queries

### VueTelescope

VueTelescope是Nuxtlabs出的一个工具，是一个检测vue网站做的工具，检测输入的站点用了哪些模块

https://www.vuetelescope.com/
