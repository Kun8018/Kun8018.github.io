---
title: React（十）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/redux.jpeg
---

​      react组件库

<!--more-->

## UI库

### shadcn/ui

shadcn/ui 并不是一个组件库，而是一个组件集合。你不需要将其安装为项目依赖项，而是通过复制粘贴到你的项目中。这种设计方式颠覆了传统组件库的设计理念，但它的好处是更容易控制代码，决定组件的构建和样式。

shadcn/ui 可以在支持 React 的框架的项目中使用，例如 Next.js、Astro、Remix、Gatsby 等。下面通过 Vite 创建一个 React 项目来体验一下

在react项目中添加shadcn-ui

```shell
npx shadcn-ui@latest init
```

这一步会生成 `components.json` 配置，以及 `src/components`、`src/lib` 两个文件夹，配置就已经完成了。

*需要注意的是，按照原文档配置的导入路径应该是 `@/components` 和 `@/lib/utils`，按照这个生成的会直接在根目录生成 `@`目录，导致无法正常导入。* 我认为本意应该是要生成在 `src` 目录下的，CLI 工具并没有处理别名 `@`成正确目录。所以我把上面步骤中对 `@`别名定义都改成了 `src`。

接下来，就可以导入组件使用啦。用 CLI 工具添加一个 `button` 组件

```shell
npx shadcn-ui@latest add button
```

这个命名会在 `src/components` 添加一个 `button` 组件代码，按常规代码导入即可使用

```tsx
import { Button } from "./components/ui/button"

function App() {
  return (
    <Button>Click me</Button>
  )
}

export default App
```

#### roadmap-ui

使用shadcn-ui构建的roadmap类的图表

https://github.com/haydenbleasel/roadmap-ui?tab=readme-ov-file

#### 21st

shadcn-ui blocks

https://github.com/serafimcloud/21st



### chakra-UI



#### ark-UI

基于zagjs的headless UI库

https://ark-ui.com/docs/react/components/checkbox



### Material-UI

推出很久 很好用

### Elastic-UI



### rsuitejs

Charts.rsuite.js



### blueprint.js



### NextUI

看起来很好看



### HeadlessUI

tailwind的公司开源的UI库



### ReachUI



### reakit



### react-Aria

React hooks组件库



### mantine

https://mantine.dev/

使用

```react
import { Badge, Button, MantineProvider } from '@mantine/core';

function Demo() {
  return (
    <MantineProvider theme={{
      fontFamily: 'Greycliff CF, sans-serif',
      colors: {
        'ocean-blue': ['#7AD1DD', '#5FCCDB', '#44CADC', '#2AC9DE', '#1AC2D9', '#11B7CD', '#09ADC3', '#0E99AC', '#128797', '#147885'],
        'bright-pink': ['#F0BBDD', '#ED9BCF', '#EC7CC3', '#ED5DB8', '#F13EAF', '#F71FA7', '#FF00A1', '#E00890', '#C50E82', '#AD1374'],
      },
    }}>
      <Button color="ocean-blue">Ocean blue button</Button>
      <Badge color="bright-pink" variant="filled">Bright pink badge</Badge>
    </MantineProvider>
  );
}
```

### core-ui

https://github.com/coreui/coreui-react/



### radix-ui

安装

```shell
npm install @radix-ui/react-popover@latest -E
```

使用

```react
import React from 'react';
import * as Popover from '@radix-ui/react-popover';
import './styles.css';

const PopoverDemo = () => (
  <Popover.Root>
    <Popover.Trigger className="PopoverTrigger">More info</Popover.Trigger>
    <Popover.Portal>
      <Popover.Content className="PopoverContent" sideOffset={5}>
        Some more info…
        <Popover.Arrow className="PopoverArrow" />
      </Popover.Content>
    </Popover.Portal>
  </Popover.Root>
);

export default PopoverDemo;
```

#### shadcn-ui

基于radix-ui和tailwindcss



### float-ui

安装

```shell
npm install @floating-ui/dom @floating-ui/react
```

使用

```react

```



### tanstack

#### query



#### table

安装

```shell
npm install @tanstack/react-table
```



#### router



#### form

安装

```shell
npm i @tanstack/react-form
```

使用

```react
import React from 'react'
import ReactDOM from 'react-dom/client'
import { useForm, FieldApi } from '@tanstack/react-form'

function FieldInfo({ field }: { field: FieldApi<any, any> }) {
  return (
    <>
      {field.state.meta.touchedError ? (
        <em>{field.state.meta.touchedError}</em>
      ) : null}{' '}
      {field.state.meta.isValidating ? 'Validating...' : null}
    </>
  )
}

export default function App() {
  const form = useForm({
    // Memoize your default values to prevent re-renders
    defaultValues: React.useMemo(
      () => ({
        firstName: '',
        lastName: '',
      }),
      [],
    ),
    onSubmit: async (values) => {
      // Do something with form data
      console.log(values)
    },
  })

  return (
    <div>
      <h1>Simple Form Example</h1>
      {/* A pre-bound form component */}
      <form.Form>
        <div>
          {/* A type-safe and pre-bound field component*/}
          <form.Field
            name="firstName"
            validate={(value) => !value && 'A first name is required'}
            validateAsyncOn="change"
            validateAsyncDebounceMs={500}
            validateAsync={async (value) => {
              await new Promise((resolve) => setTimeout(resolve, 1000))
              return (
                value.includes('error') && 'No "error" allowed in first name'
              )
            }}
            children={(field) => (
              // Avoid hasty abstractions. Render props are great!
              <>
                <label htmlFor={field.name}>First Name:</label>
                <input name={field.name} {...field.getInputProps()} />
                <FieldInfo field={field} />
              </>
            )}
          />
        </div>
        <div>
          <form.Field
            name="lastName"
            children={(field) => (
              <>
                <label htmlFor={field.name}>Last Name:</label>
                <input name={field.name} {...field.getInputProps()} />
                <FieldInfo field={field} />
              </>
            )}
          />
        </div>
        <form.Subscribe
          selector={(state) => [state.canSubmit, state.isSubmitting]}
          children={([canSubmit, isSubmitting]) => (
            <button type="submit" disabled={!canSubmit}>
              {isSubmitting ? '...' : 'Submit'}
            </button>
          )}
        />
      </form.Form>
    </div>
  )
}

const rootElement = document.getElementById('root')!
ReactDOM.createRoot(rootElement).render(<App />)
```

#### virtual



#### react-charts



### geist-ui



### Amplify UI

amazon推出的UI组件库

### fluentUI

微软推出的组件库

https://developer.microsoft.com/en-us/fluentui#/controls/web/button

### magicUI

react动画ui实现

https://magicui.design/



### lukachoui

https://ui.lukacho.com/components/accordion



### hyperui

https://github.com/markmead/hyperui



### material-tailwind

https://github.com/creativetimofficial/material-tailwind?tab=readme-ov-file



### aceternityui

https://ui.aceternity.com/components/animated-modal

### motion-primitives

https://motion-primitives.com/docs/text-shimmer

## 拖动库

### react-beautiful-dnd



```react
import { DragDropContext, Draggable, Droppable } from 'react-beautiful-dnd'

class DraggableTags extends Component {
  render() {
    return (
    	<DragDropContext onDragEnd={this.onDragEnd}>
      	<Droppable droppableId="droppable" direction="horizontal">
          {(provided, _snapshot)=> (
            <Draggable>
							{(provided, _snapshot)=> (
              	style={
                  
                }
              )}
            </Draggable>
          )}
        </Droppable>
      </DragDropContext>
    )
  }
}
```





### react-dnd



Https://juejin.cn/post/6933036276660731912　



### dnd-kit

react拖动组件

```shell
npm install @dnd-kit/core
```

拖动排序组件

```shell
npm install @dnd-kit/sortable
```

使用

```react
import React, {useState} from 'react';
import {DndContext} from '@dnd-kit/core';

import {Droppable} from './Droppable';
import {Draggable} from './Draggable';

function App() {
  const [isDropped, setIsDropped] = useState(false);
  const draggableMarkup = (
    <Draggable>Drag me</Draggable>
  );
  
  return (
    <DndContext onDragEnd={handleDragEnd}>
      {!isDropped ? draggableMarkup : null}
      <Droppable>
        {isDropped ? draggableMarkup : 'Drop here'}
      </Droppable>
    </DndContext>
  );
  
  function handleDragEnd(event) {
    if (event.over && event.over.id === 'droppable') {
      setIsDropped(true);
    }
  }
}

// Droppable.jsx
import React from 'react';
import {useDroppable} from '@dnd-kit/core';

export function Droppable(props) {
  const {isOver, setNodeRef} = useDroppable({
    id: 'droppable',
  });
  const style = {
    color: isOver ? 'green' : undefined,
  };
  
  
  return (
    <div ref={setNodeRef} style={style}>
      {props.children}
    </div>
  );
}

// Draggable.jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

export function Draggable(props) {
  const {attributes, listeners, setNodeRef, transform} = useDraggable({
    id: 'draggable',
  });
  const style = transform ? {
    transform: `translate3d(${transform.x}px, ${transform.y}px, 0)`,
  } : undefined;

  
  return (
    <button ref={setNodeRef} style={style} {...listeners} {...attributes}>
      {props.children}
    </button>
  );
}
```

### react-draggable

实现react组件拖拽功能。

`react-draggable`的拖拽通过`CSS`中的`transform: translate(x, y)`来实现目标元素的移动

安装

```
npm install react-draggable --save
```

使用

```react
import React from 'react';
import ReactDOM from 'react-dom';
import Draggable from 'react-draggable';
 
class App extends React.Component {
 
  eventLogger = (e: MouseEvent, data: Object) => {
    console.log('Event: ', e);
    console.log('Data: ', data);
  };
 
  render() {
    return (
      <Draggable
        axis="x"
        handle=".handle"
        defaultPosition={{x: 0, y: 0}}
        position={null}
        grid={[25, 25]}
        scale={1}
        onStart={this.handleStart}
        onDrag={this.handleDrag}
        onStop={this.handleStop}>
        <div>
          <div className="handle">Drag from here</div>
          <div>This readme is really dragging on...</div>
        </div>
      </Draggable>
    );
  }
}
 
ReactDOM.render(<App/>, document.body);
```

### react-rnd

拖动/放大缩小组件

```shell
npm i -S react-rnd
```

使用

```react
<Rnd
  size={{ width: this.state.width,  height: this.state.height }}
  position={{ x: this.state.x, y: this.state.y }}
  onDragStop={(e, d) => { this.setState({ x: d.x, y: d.y }) }}
  onResizeStop={(e, direction, ref, delta, position) => {
    this.setState({
      width: ref.style.width,
      height: ref.style.height,
      ...position,
    });
  }}
>
  001
</Rnd>
```



### @use-gesture/react

结合动画库react-spring实现拖动时的动画

安装

```shell
npm install @use-gesture/react
```

使用

```react
import { useSpring, animated } from '@react-spring/web'
import { useDrag } from '@use-gesture/react'

function PullRelease() {
  const [{ x, y }, api] = useSpring(() => ({ x: 0, y: 0 }))

  // Set the drag hook and define component movement based on gesture data
  const bind = useDrag(({ down, movement: [mx, my] }) => {
    api.start({ x: down ? mx : 0, y: down ? my : 0, immediate: down })
  })

  // Bind it to a component
  return <animated.div {...bind()} style={{ x, y }} />
}
```



### react-grid-layout

react拖动布局组件

安装

```shell
npm install react-grid-layout
```

使用

```react
import GridLayout from "react-grid-layout";

class MyFirstGrid extends React.Component {
  render() {
    // layout is an array of objects, see the demo for more complete usage
    const layout = [
      { i: "a", x: 0, y: 0, w: 1, h: 2, static: true },
      { i: "b", x: 1, y: 0, w: 3, h: 2, minW: 2, maxW: 4 },
      { i: "c", x: 4, y: 0, w: 1, h: 2 }
    ];
    return (
      <GridLayout
        className="layout"
        layout={layout}
        cols={12}
        rowHeight={30}
        width={1200}
      >
        <div key="a">a</div>
        <div key="b">b</div>
        <div key="c">c</div>
      </GridLayout>
    );
  }
}
```



### react-sortable-hoc

拖动排序

安装

```shell
npm install react-sortable-hoc --save
```

使用

```react
import React, {Component} from 'react';
import {render} from 'react-dom';
import {SortableContainer, SortableElement} from 'react-sortable-hoc';
import arrayMove from 'array-move';

const SortableItem = SortableElement(({value}) => <li>{value}</li>);

const SortableList = SortableContainer(({items}) => {
  return (
    <ul>
      {items.map((value, index) => (
        <SortableItem key={`item-${value}`} index={index} value={value} />
      ))}
    </ul>
  );
});

class SortableComponent extends Component {
  state = {
    items: ['Item 1', 'Item 2', 'Item 3', 'Item 4', 'Item 5', 'Item 6'],
  };
  onSortEnd = ({oldIndex, newIndex}) => {
    this.setState(({items}) => ({
      items: arrayMove(items, oldIndex, newIndex),
    }));
  };
  render() {
    return <SortableList items={this.state.items} onSortEnd={this.onSortEnd} />;
  }
}

render(<SortableComponent />, document.getElementById('root'));
```

参数说明：

axis: 拖动时的轴。默认为y，当需要有多个方向时需要指定为xy

lockAxis：锁定子项目拖动的轴，可以为x或者y

useDragHandle：使用拖动图标。不是整个子项拖动



### fullcalendar

基于日历的拖动组件

安装

```shell
$ npm install @fullcalendar/core @fullcalendar/daygrid @fullcalendar/timegrid @fullcalendar/list
```

使用

```react
import { Calendar } from '@fullcalendar/core';
import dayGridPlugin from '@fullcalendar/daygrid';
import timeGridPlugin from '@fullcalendar/timegrid';
import listPlugin from '@fullcalendar/list';
...
let calendar = new Calendar(calendarEl, {
  plugins: [ dayGridPlugin, timeGridPlugin, listPlugin ],
  initialView: 'dayGridMonth',
  headerToolbar: {
    left: 'prev,next today',
    center: 'title',
    right: 'dayGridMonth,timeGridWeek,listWeek'
  }
});

var calendar = new Calendar(calendarEl, {
  dateClick: function() {
    alert('a day has been clicked!');
  }
});
```

时间线插件

安装

```shell
npm install --save @fullcalendar/core @fullcalendar/resource-timeline
```

使用

```react
import { Calendar } from '@fullcalendar/core';
import resourceTimelinePlugin from '@fullcalendar/resource-timeline';
...
let calendar = new Calendar(calendarEl, {
  plugins: [ resourceTimelinePlugin ],
  initialView: 'resourceTimeline',
  resources: [
    // your resource list
  ]
});
```

https://github.com/fullcalendar/fullcalendar

### react-dragline

在react中拖拽组件时显示辅助线

```shell
npm install react-dragline --save
```

使用

```react
import { DraggableContainer, DraggableChild } from 'react-dragline'

class Example extends React.Component {
  state = [
    { id: 1, position: {x: 100, y: 10} },
    { id: 2, position: {x: 400, y: 200} },
  ]

  render() {
    const containerStyle = {
      height: 600,
      position: 'relative',
    }

    return (
      <DraggableContainer style={containerStyle}>
        {
          this.state.children.map(({ id, position }, index) => {
            const style = {
              width: 100,
              height: 100,
              cursor: 'move',
              background: '#8ce8df',
            }

            return (
              <DraggableChild key={id} defaultPosition={position}>
                <div style={style} />
              </DraggableChild>
            )
          })
        }
      </DraggableContainer>
    )
  }
}

ReactDOM.render(<Example />, container)
```

### react-drag-to-select

拖动时框选组件

```shell
npm install --save @air/react-drag-to-select
```

使用

```react
import { useSelectionContainer } from '@air/react-drag-to-select'

const App = () => {
  const { DragSelection } = useSelectionContainer();

  return (
    <div>
      <DragSelection/>
      <div>Selectable element</div>
    </div>
  )
}
```

### react-selecto

也是在拖动区域框选/点击选中元素

```shell
npm install react-selecto
```

使用

```react
import Selecto from "react-selecto";

<Selecto
    // The container to add a selection element
    container={document.body}
    // The area to drag selection element (default: container)
    dragContainer={window}
    // Targets to select. You can register a queryselector or an Element.
    selectableTargets={[".target", document.querySelector(".target2")]}
    // Whether to select by click (default: true)
    selectByClick={true}
    // Whether to select from the target inside (default: true)
    selectFromInside={true}
    // After the select, whether to select the next target with the selected target (deselected if the target is selected again).
    continueSelect={false}
    // Determines which key to continue selecting the next target via keydown and keyup.
    toggleContinueSelect={"shift"}
    // The container for keydown and keyup events
    keyContainer={window}
    // The rate at which the target overlaps the drag area to be selected. (default: 100)
    hitRate={100}
    onSelect={e => {
        e.added.forEach(el => {
            el.classList.add("selected");
        });
        e.removed.forEach(el => {
            el.classList.remove("selected");
        });
    }}
    />
```

### react-moveable

[moveable](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fdaybrush%2Fmoveable%21)是一个可拖动的、可调整大小的、可缩放的、可旋转的、可弯曲的、可收缩的、可分组的、可对齐的一个插件，包含了不同框架的版本

安装

```shell
export default function MoveItem () {
  const [target, setTarget] = useState(null)
  const [frame, setFrame] = useState({
      translate: [0,0],
  });
  let [snapContainer, setSnapContainer] = useState(null)
  let [dropBounds, setDropBounds] = useState([])
  useEffect(() => {
    setTarget(document.querySelector('.target'))
    setSnapContainer(document.querySelector('.main'))
    let dom = document.querySelector('.center-drop')
    // getBoundingClientRect()获取元素到窗口的距离
    dropBounds = dom.getBoundingClientRect()
    setDropBounds({
      top: 0,
      left: 0,
      right: dropBounds.width,
      bottom: dropBounds.height,
    })
  }, [])
  // 拖拽
  function handleDragStart (e) {
    e.set(frame.translate);
  }
  function handleDrag (e) {
    frame.translate = e.beforeTranslate;
    e.target.style.transform = `translate(${e.beforeTranslate[0]}px, ${e.beforeTranslate[1]}px)`;
  }
  return (
    <div className='main'>
      <div className='target'>
        
      </div>
      <Moveable
        target={target} // moveable的对象
        draggable // 是否可以拖拽
        padding={{"left":0,"top":0,"right":0,"bottom":0}} // padding距离
        zoom={1} // 缩放包裹的moveable
        origin={true} // 显示中心点
        throttleDrag={0} // 拖拽阈值 达到这个值才执行拖拽
        onDragStart={handleDragStart} // 拖动开始执行
        onDrag={handleDrag} // 拖动中
        snappable={true} // 是否初始化磁吸功能
        snapContainer={snapContainer} // 磁吸功能的容器
        bounds={dropBounds} // 边界点
      />
    </div>
  )
}
```

https://juejin.cn/post/7085174199307730951#heading-1

### plain-draggable

可以实现各种吸附、排除拖拽的效果

使用

```html
<script src="plain-draggable.min.js"></script>
<script>
draggable = new PlainDraggable(document.getElementById('draggable'));

draggable.onMove = function(newPosition) {
  console.log('left: %d top: %d width: %d height: %d it is scrolling: %s'
    // determined position that was changed by snap and onDrag
    newPosition.left, newPosition.top,
    this.rect.width, this.rect.height,
    newPosition.autoScroll);
};

draggable.onDragStart = function(pointerXY) {
  if (isEar(pointerXY)) {
    alert('Do not pull my ear!!');
    return false;
  }
  return true;
};
</script>
```



### muuri

拖动库

```shell
npm install muuri
```

### react-resizable-panels

可拖动布局

```react
import { Panel, PanelGroup, PanelResizeHandle } from "react-resizable-panels";

<PanelGroup autoSaveId="example" direction="horizontal">
  <Panel defaultSize={25}>
    <SourcesExplorer />
  </Panel>
  <PanelResizeHandle />
  <Panel>
    <SourceViewer />
  </Panel>
  <PanelResizeHandle />
  <Panel defaultSize={25}>
    <Console />
  </Panel>
</PanelGroup>
```



## markdown

###  Remarkable.js

安装

```shell
npm install remarkable --save
```

使用

首先利用 require 或 import 語法獲取 Remakable 方法，然後透過 new 生成一個 Remarkable 對象。

透過它提供的 render 方法，我們可以直接獲取 HTML

```javascript
var Remarkable = require("remarkable");
var md = new Remarkable();

console.log(md.render("# Remarkable rulezz!"));
// => <h1>Remarkable rulezz!</h1>
```

https://juejin.cn/post/6844903782342459400



## 路由相关组件

### wouter

```react
import { Link, Route } from "wouter";

const App = () => (
  <div>
    <Link href="/users/1">
      <a className="link">Profile</a>
    </Link>

    <Route path="/about">About Us</Route>
    <Route path="/users/:name">{(params) => <div>Hello, {params.name}!</div>}</Route>
    <Route path="/inbox" component={InboxPage} />
  </div>
);
```

hooks

```react
import { useRoute } from "wouter";
import { Transition } from "react-transition-group";

const AnimatedRoute = () => {
  // `match` is boolean
  const [match, params] = useRoute("/users/:id");

  return <Transition in={match}>Hi, this is: {params.id}</Transition>;
};
```



```react
import { useLocation } from "wouter";

const CurrentLocation = () => {
  const [location, setLocation] = useLocation();

  return (
    <div>
      {`The current page is: ${location}`}
      <a onClick={() => setLocation("/somewhere")}>Click to update</a>
    </div>
  );
};
```

使用

```react
import { useState, useEffect } from "react";
import { Router, Route } from "wouter";
import { useLocationProperty, navigate } from "wouter/use-location";

// returns the current hash location in a normalized form
// (excluding the leading '#' symbol)
const hashLocation = () => window.location.hash.replace(/^#/, "") || "/";

const hashNavigate = (to) => navigate('#' + to);

const useHashLocation = () => {
  const location = useLocationProperty(hashLocation);
  return [location, hashNavigate];
};

const App = () => (
  <Router hook={useHashLocation}>
    <Route path="/about" component={About} />
    ...
  </Router>
);
```

## Canvas

### react-knova

knova在react的使用，绘制canvas

```shell
npm install react-knova knova --save
```

使用

```react
import React, { Component } from 'react';
import { render } from 'react-dom';
import { Stage, Layer, Rect, Text } from 'react-konva';
import Konva from 'konva';

class ColoredRect extends React.Component {
  state = {
    color: 'green'
  };
  handleClick = () => {
    this.setState({
      color: Konva.Util.getRandomColor()
    });
  };
  render() {
    return (
      <Rect
        x={20}
        y={20}
        width={50}
        height={50}
        fill={this.state.color}
        shadowBlur={5}
        onClick={this.handleClick}
      />
    );
  }
}

class App extends Component {
  render() {
    // Stage is a div container
    // Layer is actual canvas element (so you may have several canvases in the stage)
    // And then we have canvas shapes inside the Layer
    return (
      <Stage width={window.innerWidth} height={window.innerHeight}>
        <Layer>
          <Text text="Try click on rect" />
          <ColoredRect />
        </Layer>
      </Stage>
    );
  }
}

render(<App />, document.getElementById('root'));
```



### react-canvas



### react-art



### react-canvas-draw

基于canvas实现自由绘制的库

安装

```shell
npm install react-canvas-draw --save
```

使用

```react
import React from "react";
import ReactDOM from "react-dom";
import CanvasDraw from "react-canvas-draw";

ReactDOM.render(<CanvasDraw />, document.getElementById("root"));
```



## 动画库

### react-spring

React Spring 是一个专注于拟真弹簧动效的动画库，稍加学习就可以写出高性能易维护的复杂动画。学过 React Native 的同学可能就会对 React Spring 的 API 有些熟悉，它的思想正是启发自前者内置并广泛使用的 Animated API：将动画实际的执行过程全部放在 **旁路的副作用** 之中，即在主流程之外独立执行带有副作用的动画逻辑

使用

```react
import { useRef } from 'react'
import { useSprings, animated } from '@react-spring/web'
import { useDrag } from '@use-gesture/react'
import clamp from 'lodash.clamp'
import styles from './styles.module.css'

const images = [/* ... */]

function Viewpager() {
  const index = useRef(0)
  const width = window.innerWidth
  const [props, api] = useSprings(images.length, i => ({
    x: i * width,
    scale: 1,
    display: 'block'
  }))
  const bind = useDrag(({ active, movement: [mx], cancel }) => {
    const absMx = Math.abs(mx)
    if (active && absMx > width / 3) {
      index.current -= mx / absMx
      index.current = clamp(index.current, 0, images.length - 1)
      cancel()
    }
    api.start(i => i < index.current - 1 || i > index.current + 1 ? {
      display: 'none'
    } : {
      x: (i - index.current) * width + (active ? mx : 0),
      scale: active ? 1 - absMx / width : 1,
      display: 'block'
    })
  })
  return (
    <div className={styles.wrapper}>
      {props.map(({ x, display, scale }, i) => (
        <animated.div className={styles.page} {...bind()} key={i} style={{ display, x }}>
          <animated.div style={{ scale, backgroundImage: `url(${images[i]})` }} />
        </animated.div>
      ))}
    </div>
  )
}
```

整套流程的运作基本如下：应用入口触发首次渲染，通过数据源生成 JSX 并在函数组件中管理基础的状态并启动控制动画的旁路逻辑。也就是说图中上半部的这部分主流程代码，因为不存在额外引发 React 组件重渲染的因素，所以理应会像 Stateless 组件一样只执行一次。而下半部分的旁路逻辑则应该在用户触发动画时高频率触发。

React 的思想希望理想状态下的函数都不包含副作用，用户界面总是通过 UI = fn(State) 的纯函数计算得出，这样的设计能够带来很多收益。但现实往往没办法这样美好，于是 React 提供了诸如 VDOM 协调机制、useEffects 等方式希望可控高效地在组件中使用副作用。而 react-spring 做法则是将领域特定的副作用收敛到一处并绕过主流程执行，通过各种设计巧妙避免各类预期外行为。

一般在使用 React、Vue 这类前端库时需要注意避免使用超出控制的副作用（比如直接在函数组件顶层进行事件绑定）。react-spring 的 API 设计亮点还在于它复用了 React 现有的部分机制：

- 状态使用 useRef 管理，避免 State 改变引发的全组件重渲染
- 占用子组件的 props 槽位，避免 React State 与 SpringValue 管理元素相同属性造成冲突

形式上 api.start(...) 的调用方式与 React 曾经通用的类组件范式 this.setState(...) 很相近。并且两者对值的更新都是需要通过内部调度决定时机，而不一定立刻作用到位。无关于 react-spring 的实际实现，这样做好处在于内部还可以将 “*动画逻辑代码的执行”* 和 “*实际的渲染绘制帧”* 的时机解绑，也就是说每秒 60 帧的动画实际可以只运行十几次动画逻辑，每次运行之间间隔的动画绘制可以通过 transtions 机制或是补间算法进行计算，来做到进一步的性能优化。

react-spring与rxjs配合使用

```react
import { useEffect } from 'react'
import { animated, useSpring } from '@react-spring/web'
import { fromEvent, map } from 'rxjs'

const Components = () => {
  const divIns = useRef<HTMLDivElement | null>(null)
  const from = useRef<{
    x?: number
    y?: number
  }>({})
  
   useEffect(() => {
    let subscriptionLeave: any = null
    let subscriptionEnter: any = null
    if (divIns.current) {
      const enter$ = fromEvent<React.MouseEvent>(divIns.current, 'mouseenter').pipe(
        map((event) => ({
          mouseX: event.clientX,
          mouseY: event.clientY,
        })),
      )
      const leave$ = fromEvent<React.MouseEvent>(divIns.current as HTMLDivElement, 'mouseleave')

      subscriptionEnter = enter$.subscribe(({ mouseX, mouseY }) => {
        if (divIns.current) {
          const containerRect = divIns.current.getBoundingClientRect()

          const containerCenterX = containerRect.left + containerRect.width / 2
          const containerCenterY = containerRect.top + containerRect.height / 2
          const deltaX = mouseX - containerCenterX
          const deltaY = mouseY - containerCenterY

          // 计算鼠标相对于容器的位置，决定遮罩层的动画起始位置
          let direction

          if (Math.abs(deltaX) > Math.abs(deltaY)) {
            direction = deltaX > 0 ? 'right' : 'left'
          } else {
            direction = deltaY > 0 ? 'down' : 'up'
          }

          if (direction === 'left') {
            from.current = { x: -containerRect.width, y: 0 }
            api.start({
              from: from.current,
              to: { x: 0, y: 0, opacity: 1 },
            })
          }
          if (direction === 'right') {
            from.current = { x: containerRect.width, y: 0 }
            api.start({
              from: from.current,
              to: { x: 0, y: 0, opacity: 1 },
            })
          }
          if (direction === 'up') {
            from.current = { y: -containerRect.height, x: 0 }
            api.start({
              from: from.current,
              to: { x: 0, y: 0, opacity: 1 },
            })
          }

          if (direction === 'down') {
            from.current = { y: containerRect.height, x: 0 }
            api.start({
              from: from.current,
              to: { x: 0, y: 0, opacity: 1 },
            })
          }
        }
      })

      subscriptionLeave = leave$.subscribe((event: React.MouseEvent) => {
        api.start({
          to: { ...from.current, opacity: 0 },
          from: { x: 0, y: 0 },
        })
      })
    }

    return () => {
      subscriptionLeave && subscriptionLeave.unsubscribe()
      subscriptionEnter && subscriptionEnter.unsubscribe()
    }
  }, [api])
}
```



### react-motion



### motion/react

https://github.com/motiondivision/motion

framer-motion

滚动视差

```react
import { LoremIpsum } from "./components/LoremIpsum";
import { motion, useScroll } from "framer-motion";

export default function App() {
  const { scrollYProgress } = useScroll();

  return (
    <>
      <motion.div
        className="progress-bar"
        style={{ scaleX: scrollYProgress }}
      />
      <h1>
        <code>useScroll</code> demo
      </h1>
      <LoremIpsum />
    </>
  );
}
```

`useScroll` 返回 4 个值

1、scrollX：目标容器在 x 轴方向的滚动位置，以像素为单位

2、scrollXProgress：定义的偏移量的滚动位置，这个值在 0 ~ 1` 之间变化，记录的是滚动位置与偏移总量之间的比值

3、scrollY：目标容器在 y 轴方向的滚动位置，以像素为单位

4、scrollYProgress：定义的偏移量的滚动位置，这个值在 0 ~ 1` 之间变化，记录的是滚动位置与偏移总量之间的比值

### react-transition-group

```react
import { Transition } from 'react-transition-group';
import { useRef } from 'react';

const duration = 300;

const defaultStyle = {
  transition: `opacity ${duration}ms ease-in-out`,
  opacity: 0,
}

const transitionStyles = {
  entering: { opacity: 1 },
  entered:  { opacity: 1 },
  exiting:  { opacity: 0 },
  exited:  { opacity: 0 },
};

function Fade({ in: inProp }) {
  const nodeRef = useRef(null);
  return (
    <Transition nodeRef={nodeRef} in={inProp} timeout={duration}>
      {state => (
        <div ref={nodeRef} style={{
          ...defaultStyle,
          ...transitionStyles[state]
        }}>
          I'm a fade Transition!
        </div>
      )}
    </Transition>
  );
}
```

### react-bits

https://github.com/DavidHDev/react-bits



### gsap

https://github.com/greensock/GSAP?tab=readme-ov-file



## Icons

### react-icons

包含比较流行的icons

包含antd icons、bootstrap icons、font awesome icons等图标

安装

```shell
npm install react-icons --save
```

使用

```react
import { FaBeer } from 'react-icons/fa';

class Question extends React.Component {
    render() {
        return <h3> Lets go for a <FaBeer />? </h3>
    }
}
```

https://github.com/react-icons/react-icons

### iconify



### RemixIcon

https://github.com/Remix-Design/RemixIcon



### hero-icons

https://heroicons.com/outline



## 国际化

### react-intl-universal

不建议使用react-intl，而使用React-intl-universal实现

建立英文和中文语言包,可以是json或者js文件

```javascript
const en_US = {
  'hello':'nihao',
  'name': 'zhangsan',
  'age': '30',
  'changelang': 'qiehuanyuyan',
}

export default en_US
```

中文包

```js
const zh_CN = {
  'hello':'nihao',
  'name': 'zhangsan',
  'age': '30',
  'changelang': 'qiehuanyuyan',
}

export default zh_CN
```

使用

```react
import intl from 'react-intl-universal'
import cn from '../../assets/locals/zh-CN'
import us from '../../assets'

class IntlExample extends React.Component{
  constructor(){
    super();
    this.locals = {
      'zh_CN': cn,
      'en_US': us
    }
    this.state = {
      intl: cn
    }
  }
  
  componentDidMount() {
    this.initLocale();
  }
  initLocale(locale="zh_CN"){
    
  }
}
```



### i18next-browser-languargeDetecter

检测浏览器的语言

安装

```shell
npm install i18next-browser-languagedetector
```

使用

```javascript
import i18next from 'i18next';
import LanguageDetector from 'i18next-browser-languagedetector';

i18next.use(LanguageDetector).init(i18nextOptions);
```

### react-i18next

react国际化包

安装

```shell
npm install react-i18next
```

使用

```react
import { useTranslation } from 'react-i18next';

const { i18n, t } = useTranslation();

i18n.changeLanguage(language).catch(() => {});
<html>{t('a')}</html>
```

与react-intl对比

React-i18next初始化的时候需要将初始化配置放置在初始化文件（i18n.js）中，然后将初始化文件(i18n.js)通过import的方式引入到入口文件中即可。当然也可以通过I18nextProvider将i18n往下传递到各子组件。React-intl提供的是context api初始化方案，需要将初始化配置放在IntlProvider组件中，并且将入口文件的组件（如<App />)作为IntlProvider的子组件来使用；

React-i18next提供了切换语言的接口(i18n.changeLanguage)，react-intl则需要对切换做一些封装的工作；

React-i18next提供了三种方式进行国际化操作（render props、hook和hoc)， react-intl提供了api（intl.formatMessage()）和组件(<FormattedMessage />)两种方式进行国际化；

React-i18next的语言资源文件为json格式，react-intl为js格式，同时支持变量传值；

React-i18next有很多插件可以使用比如检测当前系统语言，从后端获取数据等；

React-intl除文本翻译外还提供日期、时间和金额的国际化支持；





