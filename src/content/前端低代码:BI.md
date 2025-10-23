---
title: 前端低代码
date: 2020-10-02 21:40:33
categories: 个人生活
tags:
    - 个人生活，幸福
toc: true
thumbnail: http://cdn.kunkunzhang.top/ielts.jpeg
---

  

<!--more-->

## craftjs

craft是一个页面编辑器，可以进行各种编辑

使用

```react
import React from "react";
import {Editor, Frame, Canvas, Selector} from "@craftjs/core";
const App = () => {
  return (
    <div>
      <header>Some fancy header or whatever</header>
      <Editor>
        // Editable area starts here
        <Frame resolver={{TextComponent, Container}}>
          <Canvas>
            <TextComponent text="I'm already rendered here" />
          </Canvas>
        </Frame>
      </Editor>
    </div>
  )
}
```

对组件进行控制

```react
import {useNode} from "@craftjs/core";

const TextComponent = ({text}) => {
  const { connectors: { connect, drag }, isClicked, actions: {setProp} } = useNode(
    (state) => ({
      isClicked: state.event.selected,
    })
  );

  return (
    <div ref={dom => connect(drag(dom))}>
      <h2>{text}</h2>
      {
        isClicked ? (
          <Modal>
            <input
              type="text"
              value={text}
              onChange={e => setProp(e.target.value)}
            />
          </Modal>
        )
      }
    </div>
  )
}
```



https://github.com/prevwong/craft.js



## App-smith



## node-red

基于node的低代码平台



## nocobase



## marsview

https://github.com/JackySoft/marsview



## tango

网易出品的低代码框架

https://github.com/NetEase/tango



## tiny-engine

基于vue的低代码框架

https://github.com/opentiny/tiny-engine



## sunmao

https://github.com/smartxworks/sunmao-ui



## amis

amis是一款基于JSON配置的低代码前端框架，通过简单的JSON配置即可实现复杂的前端功能2 。它支持丰富的组件库和可视化设计器，使得开发人员可以快速构建出美观、易用的界面。同时，amis还提供了强大的扩展机制，可以轻松地集成第三方组件和插件。

https://github.com/baidu/amis?tab=readme-ov-file



## codeSandbox

CodeSandbox 则更加强大，可以视作是浏览器端的 Webpack 运行环境, 甚至在 V3 版本已经支持 VsCode 模式，支持 Vscode 的插件和 Vim 模式、还有主题

另外 CodeSandbox 支持离线运行(PWA)。基本上可以接近本地 VSCode 的编程体验. 有 iPad 的同学，也可以尝试基于它来进行开发。所以快速的原型开发我一般会直接使用 CodeSandbox

**CodeSandbox 打包和运行并不依赖于服务器, 它是完全在浏览器进行的**. 大概的结构如下:

- **Editor**: 编辑器。主要用于修改文件，CodeSandbox这里集成了 `VsCode`, 文件变动后会通知 `Sandbox` 进行转译. 计划会有文章专门介绍CodeSandbox的编辑器实现
- **Sandbox**: 代码运行器。**Sandbox 在一个单独的 iframe 中运行, 负责代码的转译(Transpiler)和运行(Evalation)**. 如最上面的图，左边是Editor，右边是Sandbox
- **Packager** 包管理器。类似于yarn和npm，负责拉取和缓存 npm 依赖

CodeSandbox 的作者 [Ives van Hoorne](https://twitter.com/CompuIves) 也尝试过将 `Webpack` 移植到浏览器上运行，因为现在几乎所有的 CLI 都是使用 Webpack 进行构建的，如果能将 Webpack 移植到浏览器上, 可以利用 Webpack 强大的生态系统和转译机制(loader/plugin)，低成本兼容各种 CLI.

然而 Webpack 太重了😱，压缩过后的大小就得 3.5MB，这还算勉强可以接受吧；更大的问题是要在浏览器端模拟 Node 运行环境，这个成本太高了，得不偿失。

所以 CodeSandbox 决定自己造个打包器，这个打包器更轻量，并且针对 CodeSandbox 平台进行优化. 比如 CodeSandbox 只关心开发环境的代码构建, 目标就是能跑起来就行了, 跟 Webpack 相比裁剪掉了以下特性:

- 生产模式. CodeSandbox 只考虑 development 模式，不需要考虑 production一些特性，比如
  - 代码压缩，优化
  - Tree-shaking
  - 性能优化
  - 代码分割
- 文件输出. 不需要打包成chunk
- 服务器通信. Sandbox直接原地转译和运行, 而Webpack 需要和开发服务器建立一个长连接用于接收指令，例如 HMR.
- 静态文件处理(如图片). 这些图片需要上传到 CodeSandbox 的服务器
- 插件机制等等.

可以认为**CodeSandbox是一个简化版的Webpack, 且针对浏览器环境进行了优化，比如使用worker来进行并行转译**

CodeSandbox 的打包器使用了接近 `Webpack Loader` 的 API, 这样可以很容易地将 Webpack 的一些 loader 移植过来. 举个例子，下面是 `create-react-app` 的实现

https://bobi.ink/2019/06/20/codesandbox/



## form

### Vform3



### formily

Formily 是阿里开源的动态化表单的解决方案，优雅的解决了多种复杂场景的表单的数据、状态管理及跨表单通信问题，同时规避了全量渲染的弊端，性能优越

 Formily基于MVVM的设计原则，常用的基础核心库有`@formily/core`、`@formily/react`、`@formily/vue`、`@formily/antd`，支持react和vue，同时支持Markup Schema、 JSX以及现在业界最流行的JSON Schema的写法

Formily 分为了**内核层**，**UI 桥接层**，**扩展组件层**，和**配置应用层**，如下图。 **内核层**是 UI 无关的，它保证了用户管理的逻辑和状态是不耦合任何一个框架。 JSON Schema 独立存在，给 UI 桥接层消费，保证了协议驱动在不同 UI 框架下的绝对一致性，不需要重复实现协议解析逻辑。 **扩展组件层**，提供一系列表单场景化组件，保证用户开箱即用。无需花大量时间做二次开发

核心优势

- 高性能
- 开箱即用
- 联动逻辑实现高效
- 跨端能力，逻辑可跨框架，跨终端复用
- 动态渲染能力

核心劣势：学习成本较高，需引多个包，包体积较大。

`@formily/core`：**ViewModel层**，负责管理表单的状态，表单校验，联动等等。

`@formily/react`：**Model层**，作为UI 库来接入内核数据，用来实现最终的表单交互效果，对于不同框架的用户，使用有不同的桥接库，这里使用`react`为例，使用vue安装`@formily/vue`。

`@formily/antd`：**View层**，在`Ant Design`基础之上封装的开箱即用的组件库。

使用

```react
import React from 'react';
import { createForm } from '@formily/core';
import { createSchemaField, FormConsumer, FormProvider } from '@formily/react';
import { FormItem, Input, Password, Submit, FormLayout, FormButtonGroup } from '@formily/antd';
import * as ICONS from '@ant-design/icons';

//用来创建表单核心领域模型，它是作为MVVM设计模式的标准 ViewModel
const form = createForm();

// 创建一个 SchemaField 组件用于解析JSON-Schema动态渲染表单的组件
const SchemaField = createSchemaField({
  // 组件列表
  components: {
    FormLayout,
    FormItem,
    Input,
    Password,
  },
  // 全局作用域，用于实现协议表达式变量注入
  scope: {
    icon(name: string) {
      return React.createElement(ICONS[name]);
    },
  },
});

/**初始化一份json schema
 * 解析 json-schema 的能力；将 json-schema 转换成 Field Model 的能力；编译 json-schema 表达式的能力
 **/
const schema = {
  // schema type
  type: 'object',
  properties: {
    layout: {
      type: 'void',
      'x-component': 'FormLayout',
      'x-component-props': {
        labelCol: 2,
        wrapperCol: 6,
        labelAlign: 'right',
        // layout: 'vertical',
      },
      // 属性描述
      properties: {
        username: {
          // schema type
          type: 'string',
          // 标题
          title: '用户名',
          // 必填
          required: true,
          // 字段 UI 包装器组件
          'x-decorator': 'FormItem',
          // 字段 UI 组件
          'x-component': 'Input',
          // 字段 UI 组件属性
          'x-component-props': {
            prefix: "{{icon('UserOutlined')}}",
          },
        },
        password: {
          type: 'string',
          title: '密码',
          required: true,
          'x-decorator': 'FormItem',
          'x-decorator-props': {
            addonAfter: '强度高',
          },
          'x-component': 'Password',
          'x-component-props': {
            prefix: "{{icon('LockOutlined')}}",
          },
        },
      },
    },
  },
};

export default () => {
  return (
    <FormProvider form={form}>
      <FormLayout layout="vertical">
        <SchemaField schema={schema} />
      </FormLayout>
      <FormButtonGroup>
        <Submit onSubmit={console.log}>提交</Submit>
      </FormButtonGroup>
      <FormConsumer>
        {() => (
          <div
            style={{
              width: 340,
              marginTop: 20,
              padding: 5,
              border: '1px dashed #666',
            }}
          >
            实时响应-用户名：{form.values.username}
          </div>
        )}
      </FormConsumer>
    </FormProvider>
  );
};
```

### json-schema-spec

https://github.com/json-schema-org/json-schema-spec



### react-jsonschema-form

https://github.com/rjsf-team/react-jsonschema-form



## Table

### drip-table

https://github.com/JDFED/drip-table

### rowy

类似于airtable的低代码库



## BI

### dataease

https://github.com/dataease/dataease



### datart

https://github.com/running-elephant/datart



### Superset

可视化平台



### Metabase

Metabase可以帮助你把数据库中的数据更好的呈现给更多人，数据分析人员通过建立一个”查询“（Metabase中定义为Question）来提炼数据，再通过仪表盘（Dashboards）来组合展示给公司成员

优点：

1.开源免费

2.工具轻量、安装依赖的环境简单、配置简单清楚

3.容易上手，操作门槛低，不会sql语句也能使用

4.支持对外共享，权限控制

5.Question可以便捷地创建图表，Dashboards界面整洁美观

缺点：

1.Question每次只能对数据库中的一张表进行查询,切换数据表已有的查询选项会重置

2.填写了sql语句的sql查询（Native query）模式不能转到点选查询（Custom）模式

3.不能在Metabase中自由转换数据表中字段的属性

4.可创建的图表类型较单一

免费 Metabase是一个免费的开源工具，并且只要你赋予权限的人都可以自由浏览你的Dashboards，Metabase虽然没有Tableau的功能多、支持的图表丰富，但Tableau使用客户端Desktop要付费，使用TableauServer发表到Tableau Online也要付费，添加可浏览的viewer也要付费(还是按人头收费，权限越高费用越高)， 而我们可能只是需要一个平台展示一些图表给同事看，为次每月要付上百美金的确得不偿失了。



https://zhuanlan.zhihu.com/p/52085283



