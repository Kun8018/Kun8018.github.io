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



## form

### Vform3





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



