---
title: React（十二）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/redux.jpeg
---

​      前端框架，快速开发页面，函数式编程，与后端api快速搭建

<!--more-->

## react库

### react-confetti

撒花特效

https://github.com/alampros/react-confetti

```jsx
import React from 'react'
import useWindowSize from 'react-use/lib/useWindowSize'
import Confetti from 'react-confetti'

export default () => {
  const { width, height } = useWindowSize()
  return (
    <Confetti
      width={width}
      height={height}
    />
  )
}
```



### react-email

放在邮件中的react组件

https://github.com/zenorocha/react-email

安装

```shell
npm install @react-email/button -E
```

使用

```react
import { Button } from '@react-email/button';

const Email = () => {
  return (
    <Button href="https://example.com" style={{ color: '#61dafb' }}>
      Click me
    </Button>
  );
};
```



### react-frame-component

在iframe里面渲染react组件

安装

```shell
npm install --save react-frame-component
```

使用

```react
import Frame from 'react-frame-component';

const Header = ({ children }) => (
  const iframeRef = React.useRef();

  React.useEffect(() => {
    // Use iframeRef for:
    // - focus managing
    // - triggering imperative animations
    // - integrating with third-party DOM libraries
    iframeRef.current.focus()
  }, [])

	return (
    <Frame>
      <h1>{children}</h1>
    </Frame>
  )
);

ReactDOM.render(<Header>Hello</Header>, document.body);
```





### lru-cache

js的lru缓存

```shell
npm install lru-cache --save
```

使用

```javascript
// hybrid module, either works
import { LRUCache } from 'lru-cache'
// or:
const { LRUCache } = require('lru-cache')
// or in minified form for web browsers:
import { LRUCache } from 'http://unpkg.com/lru-cache@9/dist/mjs/index.min.mjs'

// At least one of 'max', 'ttl', or 'maxSize' is required, to prevent
// unsafe unbounded storage.
//
// In most cases, it's best to specify a max for performance, so all
// the required memory allocation is done up-front.
//
// All the other options are optional, see the sections below for
// documentation on what each one does.  Most of them can be
// overridden for specific items in get()/set()
const options = {
  max: 500,

  // for use with tracking overall storage size
  maxSize: 5000,
  sizeCalculation: (value, key) => {
    return 1
  },

  // for use when you need to clean up something when objects
  // are evicted from the cache
  dispose: (value, key) => {
    freeFromMemoryOrWhatever(value)
  },

  // how long to live in ms
  ttl: 1000 * 60 * 5,

  // return stale items before removing from cache?
  allowStale: false,

  updateAgeOnGet: false,
  updateAgeOnHas: false,

  // async method to use for cache.fetch(), for
  // stale-while-revalidate type of behavior
  fetchMethod: async (
    key,
    staleValue,
    { options, signal, context }
  ) => {},
}

const cache = new LRUCache(options)

cache.set('key', 'value')
cache.get('key') // "value"

// non-string keys ARE fully supported
// but note that it must be THE SAME object, not
// just a JSON-equivalent object.
var someObject = { a: 1 }
cache.set(someObject, 'a value')
// Object keys are not toString()-ed
cache.set('[object Object]', 'a different value')
assert.equal(cache.get(someObject), 'a value')
// A similar object with same keys/values won't work,
// because it's a different object identity
assert.equal(cache.get({ a: 1 }), undefined)

cache.clear() // empty the cache
```



### eventbus

安装

```shell
yarn add events
```

使用

```javascript
//event.ts
import {EventEmitter} from 'events'
export default new EventEmitter()

//发布
import emitter from './event'

class Father extends React.Component {
  constructor(props){
    super(props)
  }
  handleClick = () =>{
    emitter.emit('info','来自father的info')
  }
}

export default Father
//订阅
//emitter.addListener()事件监听订阅
//emitter.removeListener()进行事件销毁，取消订阅
import emitter from './event'

class Son extends React.Component {
  constructor(props){
    super(props)
  }
}
```

### mitt

很小的发布订阅库 200b

安装

```shell
npm install --save mitt
```

使用

```javascript
import mitt from 'mitt'

const emitter = mitt()

// listen to an event
emitter.on('foo', e => console.log('foo', e) )

// listen to all events
emitter.on('*', (type, e) => console.log(type, e) )

// fire an event
emitter.emit('foo', { a: 'b' })

// clearing all events
emitter.all.clear()

// working with handler references:
function onFoo() {}
emitter.on('foo', onFoo)   // listen
emitter.off('foo', onFoo)  // unlisten
```



### react-flow

安装

```shell
npm install --save react-flow-renderer
```

使用

```react
import React from 'react';
import ReactFlow from 'react-flow-renderer';

const elements = [
  { id: '1', type: 'input', data: {lable: 'Node 1'},position: {x: 250, y: 50}},
  { id: '2', data: {lable: <div>Node 2</div>}, position: {x: 100, y: 100}},
  { id: 'el-2', source: '1', targetL '2', animated: true}
]

export default ()=> <ReactFlow elements={elements}></ReactFlow>
```

React Flow有两个背景变体：点和线。也可以通过将其作为子级传递给`ReactFlow`组件来使用它

```react
import ReactFlow, { Background } from 'react-flow-renderer';

const FlowWithBackground = () => (
  <ReactFlow elements={elements}>
    <Background
      variant="dots"
      gap={12}
      size={4}
    />
  </ReactFlow>
);
```

可以通过将mini-map插件作为子级传递给`ReactFlow`组件来使用它：

```react
import ReactFlow, { MiniMap } from 'react-flow-renderer';

const FlowWithMiniMap = () => (
  <ReactFlow elements={elements}>
    <MiniMap
      nodeColor={(node) => {
        switch (node.type) {
          case 'input': return 'red';
          case 'default': return '#00ff00';
          case 'output': return 'rgb(0,0,255)';
          default: return '#eee';
        }
      }}
    />
  </ReactFlow>
);
```

控制面板包含zoom-in、zoom-out、fit-view和一个锁定/解锁按钮。

```react
import ReactFlow, { Controls } from 'react-flow-renderer';

const FlowWithControls = () => (
  <ReactFlow elements={elements}>
    <Controls />
  </ReactFlow>
);
```

如果需要访问`ReactFlow`组件外部的React Flow的内部状态和操作，可以用`ReactFlowProvider`组件包装它

```react
import ReactFlow, { ReactFlowProvider } from 'react-flow-renderer';

const FlowWithOwnProvider = () => (
  <ReactFlowProvider>
    <ReactFlow
      elements={elements}
      onElementClick={onElementClick}
      onConnect={onConnect}
    />
  </ReactFlowProvider>
);
```

https://www.5axxw.com/wiki/content/obkffc

#### 流程树Dagrejs

配合Dagrejs可以画出自动布局的流程图

```react
import React, { useCallback } from 'react';
import ReactFlow, { addEdge, ConnectionLineType, useNodesState, useEdgesState } from 'reactflow';
import dagre from 'dagre';
import 'reactflow/dist/style.css';

import { initialNodes, initialEdges } from './nodes-edges.js';

import './index.css';

const dagreGraph = new dagre.graphlib.Graph();
dagreGraph.setDefaultEdgeLabel(() => ({}));

const nodeWidth = 172;
const nodeHeight = 36;

const getLayoutedElements = (nodes, edges, direction = 'TB') => {
  const isHorizontal = direction === 'LR';
  dagreGraph.setGraph({ rankdir: direction });

  nodes.forEach((node) => {
    dagreGraph.setNode(node.id, { width: nodeWidth, height: nodeHeight });
  });

  edges.forEach((edge) => {
    dagreGraph.setEdge(edge.source, edge.target);
  });

  dagre.layout(dagreGraph);

  nodes.forEach((node) => {
    const nodeWithPosition = dagreGraph.node(node.id);
    node.targetPosition = isHorizontal ? 'left' : 'top';
    node.sourcePosition = isHorizontal ? 'right' : 'bottom';

    // We are shifting the dagre node position (anchor=center center) to the top left
    // so it matches the React Flow node anchor point (top left).
    node.position = {
      x: nodeWithPosition.x - nodeWidth / 2,
      y: nodeWithPosition.y - nodeHeight / 2,
    };

    return node;
  });

  return { nodes, edges };
};

const { nodes: layoutedNodes, edges: layoutedEdges } = getLayoutedElements(
  initialNodes,
  initialEdges
);

const LayoutFlow = () => {
  const [nodes, setNodes, onNodesChange] = useNodesState(layoutedNodes);
  const [edges, setEdges, onEdgesChange] = useEdgesState(layoutedEdges);

  const onConnect = useCallback(
    (params) =>
      setEdges((eds) =>
        addEdge({ ...params, type: ConnectionLineType.SmoothStep, animated: true }, eds)
      ),
    []
  );
  const onLayout = useCallback(
    (direction) => {
      const { nodes: layoutedNodes, edges: layoutedEdges } = getLayoutedElements(
        nodes,
        edges,
        direction
      );

      setNodes([...layoutedNodes]);
      setEdges([...layoutedEdges]);
    },
    [nodes, edges]
  );

  return (
    <div className="layoutflow">
      <ReactFlow
        nodes={nodes}
        edges={edges}
        onNodesChange={onNodesChange}
        onEdgesChange={onEdgesChange}
        onConnect={onConnect}
        connectionLineType={ConnectionLineType.SmoothStep}
        fitView
      />
      <div className="controls">
        <button onClick={() => onLayout('TB')}>vertical layout</button>
        <button onClick={() => onLayout('LR')}>horizontal layout</button>
      </div>
    </div>
  );
};

export default LayoutFlow;
```

#### 下载图片

```react
import React from 'react';
import { toPng } from 'html-to-image';

function downloadImage(dataUrl) {
  const a = document.createElement('a');

  a.setAttribute('download', 'reactflow.png');
  a.setAttribute('href', dataUrl);
  a.click();
}

function DownloadButton() {
  const onClick = () => {
    toPng(document.querySelector('.react-flow'), {
      filter: (node) => {
        // we don't want to add the minimap and the controls to the image
        if (
          node?.classList?.contains('react-flow__minimap') ||
          node?.classList?.contains('react-flow__controls')
        ) {
          return false;
        }

        return true;
      },
    }).then(downloadImage);
  };

  return (
    <button className="download-btn" onClick={onClick}>
      Download Image
    </button>
  );
}

export default DownloadButton;
```

### gojs

https://github.com/NorthwoodsSoftware/GoJS



### react-children-utilities

返回组件中的文字

```shell
npm install --save react-children-utilities
```

使用

```react
import React from 'react';
import Children from 'react-children-utilities';

const MyComponent = ({ children }) => {
  const onlySpans = Children.filter(children, (child) => child.type === 'span');
  return <div>{onlySpans}</div>;
};
```

其他api：

 https://www.npmjs.com/package/react-children-utilities

### nuqs

同步输入框与url

```shell
```

使用

```react
import { useQueryState, parseAsInteger } from 'nuqs'

export default () => {
  const [count, setCount] = useQueryState('count', parseAsInteger)
  return (
    <>
      <pre>count: {count}</pre>
      <button onClick={() => setCount(0)}>Reset</button>
      {/* handling null values in setCount is annoying: */}
      <button onClick={() => setCount(c => c ?? 0 + 1)}>+</button>
      <button onClick={() => setCount(c => c ?? 0 - 1)}>-</button>
      <button onClick={() => setCount(null)}>Clear</button>
    </>
  )
}
```



### K-bar

k-bar可以给react部署的站点提供一个舒服的搜索框样式

安装

```shell
npm install kbar
```

使用

```react
import {
  KBarProvider,
  KBarPortal,
  KBarPositioner,
  KBarAnimator,
  KBarSearch,
  useMatches,
  NO_GROUP
} from "kbar";

function MyApp() {
  const actions = [
    {
      id: "blog",
      name: "Blog",
      shortcut: ["b"],
      keywords: "writing words",
      perform: () => (window.location.pathname = "blog"),
    },
    {
      id: "contact",
      name: "Contact",
      shortcut: ["c"],
      keywords: "email",
      perform: () => (window.location.pathname = "contact"),
    },
  ]
  
  return (
    <KBarProvider actions={actions}>
      <KBarPortal> // Renders the content outside the root node
        <KBarPositioner> // Centers the content
          <KBarAnimator> // Handles the show/hide and height animations
            <KBarSearch /> // Search input
            <RenderResults />;
          </KBarAnimator>
        </KBarPositioner>
      </KBarPortal>
      <MyApp />
    </KBarProvider>;
  );
}
```

自定义搜索结果样式

```react
import {
  // ...
  KBarResults,
  useMatches,
  NO_GROUP,
} from "kbar";

function RenderResults() {
  const { results } = useMatches();

  return (
    <KBarResults
      items={results}
      onRender={({ item, active }) =>
        typeof item === "string" ? (
          <div>{item}</div>
        ) : (
          <div
            style={{
              background: active ? "#eee" : "transparent",
            }}
          >
            {item.name}
          </div>
        )
      }
    />
  );
}
```

### uuid

uuid是通用唯一识别码(Universally Unique Identifier)的缩写。是一种软件建构辨准，亦为开发软件基金会组织在分布式计算环境领域的一部分。其目的是让分布式系统中的所有元素具有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。

UUID由一组32位数的16进制数字构成。对于UUID，就算每纳秒产生一百万个UUID，要花100亿年才会将所有UUID用完。

格式

uuid32个16进制数字用连字号分成五组来显示，所以共有36个字符

UUID版本通过M表示，当前规范有5个版本，可选值为1、2、3、4、5，这5个版本使用不同的算法，利用不同的信息产生UUID，各版本有各版本的优势，具体来说：

uuid.v1()：创建版本1(时间戳)UUID

uuid.v3()：创建版本3(md5命名空间)UUID

uuid.v4()：创建版本4(随机)UUID

uuid.v5()：创建版本5(带SHA-1的命名空间)IIOD

安装

```shell
npm install uuid 
```

使用

```javascript
import { v4 as uuidv4} from 'uuid'

uuidv4()
```

可以使用uuid进行验证登陆,未登陆状态下生产uuid

```javascript
let uuid = sessionStorage.getItem('uuid')
if(!uuid){
  sessionStorage.setItem('uuid')
}

if(getToken()){
  sessionStorage.removeItem('uuid');
}else {
  let uuid = sessionStorage.getItem('uuid');
  if(!uuid){
    sessionStorage.setItem('uuid',uuidv4());
  }
}
```



### nanoid





### react-virtualized

使用

```react
import {AutoSizer, List, CellMeasurerCache, CellMeasurer} from 'react-virtualized'

// 宽度固定
const measureCache = new CellMeasurerCache({
    fixedWidth: true,
    minHeight: 60, 
})

// 每一行内容
const rowRenderer = ({ index, key, parent, style }) => {
    const item = records[index]

    return (
        <CellMeasurer cache={measureCache} key={key} parent={parent} rowIndex={index}>
            <div style={style}>
                content
            </div>
        </CellMeasurer>
    )
}

<AutoSizer>
    {({width, height}) => (
        <List
            width={width}
            height={height}
            deferredMeasurementCache={measureCache}
            rowCount={records.length}
            rowHeight={measureCache.rowHeight}
            rowRenderer={rowRenderer}
            className={styles.list}
        />
    )}
</AutoSizer>
```

无限滚动 + 可编辑表格

```react
import {Table, Column} from 'react-virtualized'
import {Input, Empty} from 'antd'

// 防止Input组件不必要的渲染
const MemoInput = React.memo(function (props) {
    const {rowIndex, field, handleFieldChange, ...restProps} = props
    return <Input {... restProps} onChange={(e) => handleFieldChange(field, e, rowIndex)} />
})

function VirtualTable(props) {
    // ...

    // 列, 使用useCallback优化
    const nameColumn = ({cellData, rowIndex, dataKey }) => {
        // ...
        return (
            <div>
                <MemoInput
                    placeholder="请输入姓名"
                    value={cellData}
                    rowIndex={rowIndex}
                    field="姓名"
                    handleFieldChange={handleFieldChange}
                />
            </div>
        )
    }
    
    // 表头
    const columnHeaderRenderer = useCallback(({dataKey}) => dataKey, [])

    const rowGetter = useCallback(({index}) => dataSource[index], [dataSource])

    const noRowsRenderer = useCallback(() => <Empty className={styles.empty} />, [])

    return <Table
        ref={tableRef}
        className={styles.virtualTable}
        headerClassName={styles.header}
        rowClassName={styles.row}
        headerHeight={TableHeaderHeight}
        width={TableWidth}
        height={TableHeight}
        noRowsRenderer={noRowsRenderer}
        rowHeight={TableRowHeight}
        rowGetter={rowGetter}
        rowCount={dataSource.length}
        overscanRowCount={OverscanRowCount}
    >
        <Column
            width={120}
            dataKey="姓名"
            headerRenderer={columnHeaderRenderer}
            cellRenderer={nameColumn}
        />
    </Table>
}
```



### react-window

react虚拟列表库 
React Window是一个有效呈现大型列表和表格数据的组件，是React-virtualized的完全重写。

React Window专注于使软件包更小，更快，同时API（和文档）对初学者尽可能友好。

安装

```shell
npm i react-window
```

固定高度列表

```react
import { FixedSizeList as List } from 'react-window';

const Row = ({ index, style }) => (
  <div style={style}>Row {index}</div>
);

const Example = () => (
  <List
    height={150}
    itemCount={1000}
    itemSize={35}
    width={300}
  >
    {Row}
  </List>
);
```

VariableSizeList （可变尺寸列表）

```react
import { VariableSizeList } from 'react-window';
 
const rowHeights = new Array(1000)
  .fill(true)
  .map(() => 25 + Math.round(Math.random() * 50));
 
const getItemSize = index => rowHeights[index]; // 此处采用随机数作为每个列表项的高度
 /** 
    * 每个列表项的组件
    * @param index：列表项的下标；style：列表项的样式（此参数必须传入列表项的组件中，否则会出现滚动到下方出现空白的情况）
    **/ 
const Row = ({ index, style }) => (
  <div style={style}>Row {index}</div>
);
 
const Example = () => (
  <VariableSizeList
    height={150} // 列表可视区域的高度
    itemCount={1000} // 列表数据长度
    itemSize={getItemSize} // 设置列表项的高度
    layout= "vertical" // （vertical/horizontal） 默认为vertical，此为设置列表的方向
    width={300}
  >
    {Row}
  <VariableSizeList>
);
```

结合react-virtualized-auto-sizer使列表自适应当前页面的宽高

```react
import { FixedSizeList } from "react-window";
import AutoSizer from "react-virtualized-auto-sizer";
const Example = () => (
  <AutoSizer>
    {({ height, width }) => (
      <FixedSizeList
        className="List"
        height={height}
        itemCount={1000}
        itemSize={35}
        width={width}
      >
        {Row}
      </FixedSizeList>
    )}
  </AutoSizer>
);
```

### react-virtuoso

虚拟列表/表格库

安装

```shell
npm install react-virtuoso
```

使用

```react
import * as React from 'react'
import * as ReactDOM from 'react-dom'
import { Virtuoso } from 'react-virtuoso'

const App = () => {
  return <Virtuoso style={{ height: '400px' }} totalCount={200} itemContent={index => <div>Item {index}</div>} />
}

ReactDOM.render(<App />, document.getElementById('root'))
```



### react-sticky

让组件实现类似position-sticky的效果

安装

```shell
npm install react-sticky
```

使用

```react
import React from 'react';
import { StickyContainer, Sticky } from 'react-sticky';

class App extends React.Component {
  render() {
    return (
      <StickyContainer>
        {/* Other elements can be in between `StickyContainer` and `Sticky`,
        but certain styles can break the positioning logic used. */}
        <Sticky>
          {({
            style,
 
            // the following are also available but unused in this example
            isSticky,
            wasSticky,
            distanceFromTop,
            distanceFromBottom,
            calculatedHeight
          }) => (
            <header style={style}>
              {/* ... */}
            </header>
          )}
        </Sticky>
        {/* ... */}
      </StickyContainer>
    );
  },
}
```

sticky上可以添加不同的属性

```react
<StickyContainer>
  ...
  <Sticky topOffset={80} bottomOffset={80} disableCompensation>
    { props => (...) }
  </Sticky>
  ...
</StickyContainer>
```



### react-pro-sidebar

侧边栏

https://github.com/azouaoui-med/react-pro-sidebar



### crypto-browserify

加密



### react-text-loop

react文字循环的小组件

安装

```shell
npm install react-text-loop
```

使用

```react
import TextLoop from "react-text-loop";
import Link from "react-router";
import { BodyText } from "./ui";

class App extends Component {
    render() {
        return (
            <h2>
                <TextLoop>
                    <span>First item</span>
                    <Link to="/">Second item</Link>
                    <BodyText>Third item</BodyText>
                </TextLoop>{" "}
                and something else.
            </h2>
        );
    }
}
```





### js-cookie

cookie插件

```shell
npm install js-cookie --save
```

引用

```javascript
import Cookies from 'js-cookie'

//设置cookie
Cookies.set('name','value',{expire:7,path:''}); //7天过期
Cookies.set('name',{foo:'bar'}); //设置一个json
//获取cookie
Cookies.get('name'); //获取cookie
Cookies.get();  //读取所有cookie

//删除cookie
Cookies.remove('name'); //删除cookie
```



### react-color

react-color是一个拾色器，通过它获取颜色值

安装

```shell
npm i react-color -S
```

使用

```react
import { TwitterPicker } from 'react-dom'

function () {
  render() {
    <TwitterPicker 
      width="240px"
      
      />
  }
}
```



### Ladda

按钮组件

安装

```shell
npm install ladda
```

使用

```react
import * as Ladda from 'ladda';

// Create a new instance of ladda for the specified button
var l = Ladda.create(document.querySelector('.my-button'));

// Start loading
l.start();

// Will display a progress bar for 50% of the button width
l.setProgress(0.5);

// Stop loading
l.stop();

// Toggle between loading/not loading states
l.toggle();

// Check the current state
l.isLoading();

// Delete the button's ladda instance
l.remove();
```



### Million.js

Millionjs是一个轻量级的虚拟DOM库。

据开发者称像React和Svelte的结合体，不需要编译

安装

```shell
npm install million
```

使用

m()函数创建虚拟dom，render()渲染

```react
import { m, className, style } from 'million';

const vnode = m('div', { key: 'foo' }, ['Hello World']);

const vnode = m(
  'div',
  {
    className: className({ class1: true, class2: false, class3: 1 + 1 === 2 }),
    style: style({ color: 'black', 'font-weight': 'bold' }),
  },
  ['Hello World'],
);

import { _, m, render } from 'million';

let seconds = 0;

setInterval(() => {
  render(document.body, m(vnode));
  seconds++;
}, 1000);
```

createElement创建元素

```react
import { m, createElement, Flags } from 'million';

const vnode = m(
  'div',
  { id: 'app' },
  ['Hello World'],
  Flags.ELEMENT_TEXT_CHILDREN,
);
const el = createElement(vnode);

document.body.appendChild(el);
```

#### 适配jsx

在.bablerc中配置插件

```rc
{
  "plugins": [
    [
      "@babel/plugin-transform-react-jsx",
      {
        "runtime": "automatic",
        "importSource": "million"
      }
    ]
  ]
}
```

### toast-ui-Editor

Markdown js编辑器

有纯js 版本、react版本和vue版本

安装

```shell
npm install --save @toast-ui/react-editor
```

使用

```javascript
import '@toast-ui/editor/dist/toastui-editor.css';

import { Editor } from '@toast-ui/react-editor';

class MyComponent extends React.Component {
  editorRef = React.createRef();

  handleClick = () => {
    this.editorRef.current.getInstance().exec('Bold');
  };

  handleFocus = () => {
    console.log('focus!!');
  };
  render() {
    return (
      <>
        <Editor
          previewStyle="vertical"
          height="400px"
          initialEditType="markdown"
          initialValue="hello"
          ref={this.editorRef}
  				onFocus={this.handleFocus}
        />
        <button onClick={this.handleClick}>make bold</button>
      </>
    );
  }
}
```

### react-cool-dimensions

react中响应式显示宽高的组件

```react
import useDimensions from "react-cool-dimensions";

const Card = () => {
  const { observe, currentBreakpoint } = useDimensions({
    // The "currentBreakpoint" will be the object key based on the target's width
    // for instance, 0px - 319px (currentBreakpoint = XS), 320px - 479px (currentBreakpoint = SM) and so on
    breakpoints: { XS: 0, SM: 320, MD: 480, LG: 640 },
    // Will only update the state on breakpoint changed, default is false
    updateOnBreakpointChange: true,
    onResize: ({ currentBreakpoint }) => {
      // Now the event callback will be triggered when breakpoint is changed
      // we can also access the "currentBreakpoint" here
    },
  });

  return (
    <div class={`card ${currentBreakpoint}`} ref={observe}>
      <div class="card-header">I'm 😎</div>
      <div class="card-body">I'm 👕</div>
      <div class="card-footer">I'm 👟</div>
    </div>
  );
};
```

也可以使用polyfill的resize-observer包

```react
import { ResizeObserver, ResizeObserverEntry } from "@juggle/resize-observer";

if (!("ResizeObserver" in window)) {
  window.ResizeObserver = ResizeObserver;
  // Only use it when you have this trouble: https://github.com/wellyshen/react-cool-dimensions/issues/45
  // window.ResizeObserverEntry = ResizeObserverEntry;
}
```

### react-wrap-balancer

文本对齐组件

安装

```shell
npm install react-wrap-balancer
```

使用

```react
import Balancer from 'react-wrap-balancer'

// ...

<h1>
  <Balancer>My Title</Balancer>
</h1>
```

也可以使用Provider包裹所有的组建

```react
import { Provider } from 'react-wrap-balancer'

// ...

<Provider>
  <App/>
</Provider>
```



### HyperFormula

像excel一样操作数据，适合特殊场景下

安装

```shell
npm install hyperformula
```

使用

```javascript
import { HyperFormula } from 'hyperformula';

// define the options
const options = {
  licenseKey: 'gpl-v3',
};

// define the data
const data = [['10', '20', '30', '=SUM(A1:C1)']];

// build an instance with defined options and data 
const hfInstance = HyperFormula.buildFromArray(data, options);

// call getCellValue to get the calculation results
const mySum = hfInstance.getCellValue({ col: 3, row: 0, sheet: 0 });

// print the result in the browser's console
console.log(mySum);
```



### why-did-you-render

检查react代码，避免不必要的渲染，可以使用在react16 react17和react18， RN中也同样可以使用

安装

```shell
npm install @welldone-software/why-did-you-render --save-dev
```

使用

创建一个wdyr.js, 并在应用的最开始引入

```javascript
import React from 'react';

if (process.env.NODE_ENV === 'development') {
  const whyDidYouRender = require('@welldone-software/why-did-you-render');
  whyDidYouRender(React, {
    trackAllPureComponents: true,
  });
}
```



### deku

虚拟dom库

安装

```shell
npm install --save deku
```

使用

```react
/** @jsx element */
import {element, createApp} from 'deku'
import {createStore} from 'redux'
import reducer from './reducer'

// Dispatch an action when the button is clicked
let log = dispatch => event => {
  dispatch({
    type: 'CLICKED'
  })
}

// Define a state-less component
let MyButton = {
  render: ({ props, children, dispatch }) => {
    return <button onClick={log(dispatch)}>{children}</button>
  }
}

// Create a Redux store to handle all UI actions and side-effects
let store = createStore(reducer)

// Create an app that can turn vnodes into real DOM elements
let render = createApp(document.body, store.dispatch)

// Update the page and add redux state to the context
render(
  <MyButton>Hello World!</MyButton>,
  store.getState()
)
```



### react-charts-2

react图表组件

```shell
pnpm add react-chartjs-2 chart.js
# or
yarn add react-chartjs-2 chart.js
# or
npm i react-chartjs-2 chart.js
```

使用

```react
import { Doughnut } from 'react-chartjs-2';

<Doughnut data={...} />
```



### await-to-js

捕获await的错误，相当于try-catch的语法糖

```shell
npm i await-to-js --save
```

使用

```javascript
import to from 'await-to-js';
// If you use CommonJS (i.e NodeJS environment), it should be:
// const to = require('await-to-js').default;

async function asyncTaskWithCb(cb) {
     let err, user, savedTask, notification;

     [ err, user ] = await to(UserModel.findById(1));
     if(!user) return cb('No user found');

     [ err, savedTask ] = await to(TaskModel({userId: user.id, name: 'Demo Task'}));
     if(err) return cb('Error occurred while saving task');

    if(user.notificationsEnabled) {
       [ err ] = await to(NotificationService.sendNotification(user.id, 'Task Created'));
       if(err) return cb('Error while sending notification');
    }

    if(savedTask.assignedUser.id !== user.id) {
       [ err, notification ] = await to(NotificationService.sendNotification(savedTask.assignedUser.id, 'Task was created for you'));
       if(err) return cb('Error while sending notification');
    }

    cb(null, savedTask);
}

async function asyncFunctionWithThrow() {
  const [err, user] = await to(UserModel.findById(1));
  if (!user) throw new Error('User not found');
  
}
```



### qs

解析路由 queryString

```javascript
var qs = require('qs');
var assert = require('assert');

var obj = qs.parse('a=c');
assert.deepEqual(obj, { a: 'c' });

var str = qs.stringify(obj);
assert.equal(str, 'a=c');
```

https://github.com/ljharb/qs



### redoc

生成react swagger文档



### quickLink

react中做prefetch的组件库

```shell
npm install quicklink webpack-route-manifest --save-dev
```

使用

```javascript
import { withQuicklink } from 'quicklink/dist/react/hoc.js';

const options = {
  origins: []
};

<Suspense fallback={<div>Loading...</div>}>
  <Route path="/" exact component={withQuicklink(Home, options)} />
  <Route path="/blog" exact component={withQuicklink(Blog, options)} />
  <Route path="/blog/:title" component={withQuicklink(Article, options)} />
  <Route path="/about" exact component={withQuicklink(About, options)} />
</Suspense>
```



### Guess.js





### 更多组件

在写前端的前两年，我总是热衷于找寻各种各样的组件，试图不断地充实自己使用过的组件库

但是随着前端技术的积累，我发现使用组件总是很简单的，难的是体会组件的设计思路、并能够自己根据实际情况进行修改

所以提醒自己还需潜心修炼js和多写不同场景的代码 只是沉迷组件的话。进步不大

https://ant.design/docs/react/recommendation-cn

Ant design站点下有推荐社区比较好的组件，可以在其中找到合适的组件使用
