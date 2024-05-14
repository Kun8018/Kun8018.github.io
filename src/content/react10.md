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

## Form组件

### react-hook-form

简单好看的react form表单

安装

```shell
npm install react-hook-form
```

使用

```react
import React from 'react';
import { useForm } from 'react-hook-form';

function App() {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm();
  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('firstName')} /> {/* register an input */}
      <input {...register('lastName', { required: true })} />
      {errors.lastName && <p>Last name is required.</p>}
      <input {...register('age', { pattern: /\d+/ })} />
      {errors.age && <p>Please enter number for age.</p>}
      <input type="submit" />
    </form>
  );
}
```

### redux-form

使用redux中的state管理form

```shell
npm install --save redux-form
```

使用

创建form的reducer

```react
import { createStore, combineReducers } from 'redux'
import { reducer as formReducer } from 'redux-form'

const rootReducer = combineReducers({
  // ...your other reducers here
  // you have to pass formReducer under 'form' key,
  // for custom keys look up the docs for 'getFormState'
  form: formReducer
})
```

使用reducer

```react
import React from 'react'
import { Field, reduxForm } from 'redux-form'

let ContactForm = props => {
  const { handleSubmit } = props
  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="firstName">First Name</label>
        <Field name="firstName" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="lastName">Last Name</label>
        <Field name="lastName" component="input" type="text" />
      </div>
      <div>
        <label htmlFor="email">Email</label>
        <Field name="email" component="input" type="email" />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}

ContactForm = reduxForm({
  // a unique name for the form
  form: 'contact'
})(ContactForm)
```

在外部的组件中使用该form组件

```react
import React from 'react'
import ContactForm from './ContactForm'

class ContactPage extends React.Component {
  submit = values => {
    // print the form values to the console
    console.log(values)
  }
  render() {
    return <ContactForm onSubmit={this.submit} />
  }
}
```



### Formik

安装

```shell
npm install formik --save
```

使用

```react
 // Render Prop
 import React from 'react';
 import { Formik, Form, Field, ErrorMessage } from 'formik';
 
 const Basic = () => (
   <div>
     <h1>Any place in your app!</h1>
     <Formik
       initialValues={{ email: '', password: '' }}
       validate={values => {
         const errors = {};
         if (!values.email) {
           errors.email = 'Required';
         } else if (
           !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i.test(values.email)
         ) {
           errors.email = 'Invalid email address';
         }
         return errors;
       }}
       onSubmit={(values, { setSubmitting }) => {
         setTimeout(() => {
           alert(JSON.stringify(values, null, 2));
           setSubmitting(false);
         }, 400);
       }}
     >
       {({ isSubmitting }) => (
         <Form>
           <Field type="email" name="email" />
           <ErrorMessage name="email" component="div" />
           <Field type="password" name="password" />
           <ErrorMessage name="password" component="div" />
           <button type="submit" disabled={isSubmitting}>
             Submit
           </button>
         </Form>
       )}
     </Formik>
   </div>
 );
 
 export default Basic;
```



### react-final-form

安装

```shell
npm install --save final-form react-final-form
```

使用

```react
import { Form, Field } from 'react-final-form'

const MyForm = () => (
  <Form
    onSubmit={onSubmit}
    validate={validate}
    render={({ handleSubmit }) => (
      <form onSubmit={handleSubmit}>
        <h2>Simple Default Input</h2>
        <div>
          <label>First Name</label>
          <Field name="firstName" component="input" placeholder="First Name" />
        </div>

        <h2>An Arbitrary Reusable Input Component</h2>
        <div>
          <label>Interests</label>
          <Field name="interests" component={InterestPicker} />
        </div>

        <h2>Render Function</h2>
        <Field
          name="bio"
          render={({ input, meta }) => (
            <div>
              <label>Bio</label>
              <textarea {...input} />
              {meta.touched && meta.error && <span>{meta.error}</span>}
            </div>
          )}
        />

        <h2>Render Function as Children</h2>
        <Field name="phone">
          {({ input, meta }) => (
            <div>
              <label>Phone</label>
              <input type="text" {...input} placeholder="Phone" />
              {meta.touched && meta.error && <span>{meta.error}</span>}
            </div>
          )}
        </Field>

        <button type="submit">Submit</button>
      </form>
    )}
  />
)
```

### Yup

验证器，用在form上

使用

```react
import { object, string, number, date, InferType } from 'yup';

let userSchema = object({
  name: string().required(),
  age: number().required().positive().integer(),
  email: string().email(),
  website: string().url().nullable(),
  createdOn: date().default(() => new Date()),
});

// parse and assert validity
const user = await userSchema.validate(await fetchUser());

type User = InferType<typeof userSchema>;
/* {
  name: string;
  age: number;
  email?: string | undefined
  website?: string | null | undefined
  createdOn: Date
}*/
```

### vest

声明式form验证库

使用

```javascript
import { create, test, enforce, only, warn, include, skipWhen } from "vest";
import wait from "wait";

const suite = create((data = {}, currentField) => {
  only(currentField);
  include("confirm").when("password");

  test("username", "Username is required", () => {
    enforce(data.username).isNotEmpty();
  });
  test("username", "Username is too short", () => {
    enforce(data.username).longerThan(2);
  });

  test.memo(
    "username",
    "Username already taken",
    () => {
      return doesUserExist(data.username);
    },
    [data.username]
  );

  test("password", "Password is required", () => {
    enforce(data.password).isNotEmpty();
  });
  test("password", "Password is too short", () => {
    enforce(data.password).longerThan(2);
  });
  test("password", "Password is weak. maybe add a number", () => {
    warn();
    enforce(data.password).matches(/[0-9]/);
  });

  // This means that "confirm" will not fail
  // before we start typing in it - even though it runs with "password"
  skipWhen(!data.confirm,() => {
    test("confirm", "Passwords do not match", () => {
      enforce(data.confirm).equals(data.password);
    });
  });

  // This test will only be evaluated once confirm had previous test runs
  // so that it will not light up unnecessarily 
  skipWhen(!suite.isTested("confirm"), () => {
    test("confirm", "Please confirm the password", () => {
      enforce(data.confirm).isNotEmpty();
    });
  })

  test("tos", () => {
    enforce(data.tos).isTruthy();
  });
});

export default suite;

async function doesUserExist(username) {
  await wait(1000);

  // fake taken username.
  enforce(parseInt(btoa(username), 36) % 3).notEquals(0);
}
```

引入组件中

```javascript
import React, { useState } from "react";
import Input from "./components/Input";
import Checkbox from "./components/Checkbox";
import Submit from "./components/Submit";

import classnames from "vest/classnames";
import suite from "./suite";
import "./styles.css";

export default function Form() {
  const [formstate, setFormstate] = useState({});
  const [, setUserNameLoading] = useState(false);

  const handleChange = (currentField, value) => {
    const nextState = { ...formstate, [currentField]: value };
    const result = suite(nextState, currentField);
    setFormstate(nextState);

    if (currentField === "username") {
      setUserNameLoading(true);
    }

    result.done(() => {
      setUserNameLoading(false);
    });
  };

  const cn = classnames(suite.get(), {
    invalid: "error",
    valid: "success",
    warning: "warning"
  });

  return (
    <form onSubmit={e => e.preventDefault()}>
      <Input
        name="username"
        onChange={handleChange}
        messages={suite.getErrors("username")}
        className={cn("username")}
        pending={suite.isPending("username")}
      />
      <Input
        name="password"
        onChange={handleChange}
        messages={suite
          .getErrors("password")
          .concat(suite.getWarnings("password"))}
        className={cn("password")}
      />
      <Input
        name="confirm"
        onChange={handleChange}
        messages={suite.getErrors("confirm")}
        className={cn("confirm")}
      />
      <Checkbox
        onChange={handleChange}
        name="tos"
        label="I have read and agreed to the terms of service"
        className={cn("tos")}
      />
      <Submit disabled={!suite.isValid()} />
    </form>
  );
}
```

## 路由相关组件

### wouter

```javascript
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

```javascript
import { useRoute } from "wouter";
import { Transition } from "react-transition-group";

const AnimatedRoute = () => {
  // `match` is boolean
  const [match, params] = useRoute("/users/:id");

  return <Transition in={match}>Hi, this is: {params.id}</Transition>;
};
```



```javascript
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

```javascript
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

### react-motion



## 功能组件

### echarts-for-react

安装

```shell
npm install --save echarts-for-react
```

使用

```react
import React from 'react';
import ReactECharts from 'echarts-for-react';  // or var ReactECharts = require('echarts-for-react');

<ReactECharts
  option={this.getOption()}
  notMerge={true}
  lazyUpdate={true}
  theme={"theme_name"}
  onChartReady={this.onChartReadyCallback}
  onEvents={EventsDict}
  opts={}
/>
```



### react-fiber-traverse

在fiber树中返回react node

安装

```shell
npm install react-fiber-traverse --save
```

使用

```jsx
import React from "react";
import { render } from "react-dom";
 
import { findNodeByComponentName, Utils } from "react-fiber-traverse";
 
// Sample component
// Say, if SomeComponentName looks like this -
function SomeComponentName() {
  return <div>Some text</div>;
}
 
// Render component
const rootElement = document.getElementById("root");
render(<SomeComponentName />, rootElement);
 
// Get root node
const rootFiberNode = Utils.getRootFiberNodeFromDOM(rootElement);
 
// Get component node
const someFiberNode = findNodeByComponentName(
  rootFiberNode,
  "SomeComponentName"
); // <- returns FiberNode for first usage of 'SomeComponentName'
 
console.log(someFiberNode.child.stateNode); // <- returns reference to the div
console.log(someFiberNode.child.stateNode.innerText); // <- returns 'Some text'
```



### @monaco-editor/react

在页面内插入文本编辑器，可以提供代码高亮、错误提示等功能

```shell
npm install @monaco-editor/react 
```

使用

```javascript
import Editor, { DiffEditor, useMonaco, loader } from "@monaco-editor/react";

function App() {
  const editorRef = useRef(null);

  useEffect(() => {
    if (monaco) {
      console.log("here is the monaco isntance:", monaco);
    }
  }, [monaco]);
  
  function handleEditorWillMount(monaco) {
    // here is the monaco instance
    // do something before editor is mounted
    monaco.languages.typescript.javascriptDefaults.setEagerModelSync(true);
  }
  
  function handleEditorDidMount(editor, monaco) {
    editorRef.current = editor; 
  }
  
  function handleEditorChange(value, event) {
    console.log("here is the current model value:", value);
  }
  
  function showValue() {
    alert(editorRef.current.getValue());
  }

  return (
   <>
     <button onClick={showValue}>Show value</button>
     <Editor
       height="90vh"
       defaultLanguage="javascript"
       defaultValue="// some comment"
       beforeMount={handleEditorWillMount}
       onMount={handleEditorDidMount}
			 onChange={handleEditorChange}
     />
   </>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```



### react-router-cache-route

路由缓存

```javascript
import React from 'react'
import { HashRouter as Router, Route } from 'react-router-dom'
import CacheRoute, { CacheSwitch } from 'react-router-cache-route'

import List from './views/List'
import Item from './views/Item'

const App = () => (
  <Router>
    <CacheSwitch>
      <CacheRoute exact path="/list" component={List} />
      <Route exact path="/item/:id" component={Item} />
      <Route render={() => <div>404 Not Found</div>} />
    </CacheSwitch>
  </Router>
)

export default App
```

### react-keep-alive

保持当前页面的组件不被卸载

安装

```shell
npm install --save react-keep-alive
```

使用

```react
import React from 'react';
import ReactDOM from 'react-dom';
import {
  Provider,
  KeepAlive,
} from 'react-keep-alive';
import Test from './views/Test';

ReactDOM.render(
  <Provider>
    <KeepAlive name="Test">
      <Test />
    </KeepAlive>
  </Provider>,
  document.getElementById('root'),
);
```



### react-activation

React-activation类似于vue中的keep-alive，实现组件缓存

```javascript
// App.js

import React, { useState } from 'react'
import KeepAlive from 'react-activation'

function Counter() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>count: {count}</p>
      <button onClick={() => setCount(count => count + 1)}>Add</button>
    </div>
  )
}

function App() {
  const [show, setShow] = useState(true)

  return (
    <div>
      <button onClick={() => setShow(show => !show)}>Toggle</button>
      {show && (
        <KeepAlive>
          <Counter />
        </KeepAlive>
      )}
    </div>
  )
}

export default App
```

可以在组件缓存或者卸载时做一些回调

```javascript
import KeepAlive, { useActivate, useUnactivate, withActivation } from 'react-activation'

@withActivation
class TestClass extends Component {
  ...
  componentDidActivate() {
    console.log('TestClass: componentDidActivate')
  }

  componentWillUnactivate() {
    console.log('TestClass: componentWillUnactivate')
  }
  ...
}
...
function TestFunction() {
  useActivate(() => {
    console.log('TestFunction: didActivate')
  })

  useUnactivate(() => {
    console.log('TestFunction: willUnactivate')
  })
  ...
}
...
function App() {
  ...
  return (
    {show && (
      <KeepAlive>
        <TestClass />
        <TestFunction />
      </KeepAlive>
    )}
  )
}
```



### classnames

当react原生动态添加多个className时就会报错，这时我们就可以利用classnames库添加多个className,这也是react官方推荐使用

安装

```shell
npm install classnames --save
```

支持动态导入

```react
import classnames from 'classnames'

<div className=classnames({
    'class1': true,
    'class2': true
    )>
</div>    
```

支持class动态传入变量，或者传入数组

```react
import classNames from 'classnames';

render() {
  const classStr = classNames({
    'class1': true,
    'class2': this.props.isCompleted,
    'class3': !this.props.isCompleted
    [a]: this.props.isCompleted
  });
  return (<div className={classStr}></div>);
}
```

### clsx

和classnames功能一样 但是体积更小

安装

```shell
npm install --save clsx
```

使用

```react
import clsx from 'clsx';
// or
import { clsx } from 'clsx';

// Strings (variadic)
clsx('foo', true && 'bar', 'baz');
//=> 'foo bar baz'

// Objects
clsx({ foo:true, bar:false, baz:isTrue() });
//=> 'foo baz'

// Objects (variadic)
clsx({ foo:true }, { bar:false }, null, { '--foobar':'hello' });
//=> 'foo --foobar'

// Arrays
clsx(['foo', 0, false, 'bar']);
//=> 'foo bar'

// Arrays (variadic)
clsx(['foo'], ['', 0, false, 'bar'], [['baz', [['hello'], 'there']]]);
//=> 'foo bar baz hello there'

// Kitchen sink (with nesting)
clsx('foo', [1 && 'bar', { baz:false, bat:null }, ['hello', ['world']]], 'cya');
//=> 'foo bar hello world cya'
```





### react-hot-loader

React-Hot-Loader 使用了 Webpack HMR API，针对 React 框架实现了对单个 component 的热替换，并且能够保持组件的 state。
React-Hot-Loader 在编译时会在每一个 React component 外封装一层，每一个这样的封装都会注册自己的 module.hot.accept 回调，它们会监听每一个 component 的更新，在当前 component 代码更新时只替换自己的模块，而不是整个替换 root component。
同时，React-Hot-Loader 对 component 的封装还会代理 component 的 state，所以当 component 替换之后依然能够保持之前的 state。

安装

```shell
npm install --save-dev react-hot-loader
```

 hot-loader 是基于 webpack-dev-server，所以还得安装 webpack-dev-server

```shell
npm install --save-dev webpack-dev-server
```

首先还是要让 webpack-dev-server 打开。

```javascript
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.config');

new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  historyApiFallback: true
}).listen(3000, 'localhost', function (err, result) {
  if (err) {
    return console.log(err);
  }

  console.log('Listening at http://localhost:3000/')
});
```

然后在 webpack 的配置文件里添加 react-hot-loader。

```javascript
var webpack = require('webpack');

module.exports = {
  // 修改 entry
  entry: [
    // 写在入口文件之前
    "webpack-dev-server/client?http://0.0.0.0:3000",
    "webpack/hot/only-dev-server",
    // 这里是你的入口文件
    "./src/app.js",
  ],
  output: {
    path: __dirname,
    filename: "build/js/bundle.js",
    publicPath: "/build"
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        // 在这里添加 react-hot，注意这里使用的是loaders，所以不能用 query，应该把presets参数写在 babel 的后面
        loaders: ['react-hot', 'babel?presets[]=react,presets[]=es2015']
      }
    ]
  },
  // 添加插件
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
```



### react-hot-toast

全屏的通知组件

安装

```shell
npm install react-hot-toast
```

使用

```react
import toast, { Toaster } from 'react-hot-toast';

const notify = () => toast('Here is your toast.');

const App = () => {
  return (
    <div>
      <button onClick={notify}>Make me a toast</button>
      <Toaster />
    </div>
  );
};
```



### remotion

用react写video

使用

```react
import { useCurrentFrame } from "remotion";
 
export const MyVideo = () => {
  const frame = useCurrentFrame();
 
  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "7em",
      }}
    >
      The current frame is {frame}.
    </div>
  );
};
```

配置帧数

```react
import { useVideoConfig } from "remotion";
 
export const MyVideo = () => {
  const { fps, durationInFrames, width, height } = useVideoConfig();
 
  return (
    <div
      style={{
        flex: 1,
        textAlign: "center",
        fontSize: "7em",
       }}
      >
      This {width}px x {height}px video is {durationInFrames / fps} seconds long.
    </div>
  );
};
```

### react-player

react视频播放组件，比原生的h5 video标签好用

安装

```shell
npm install react-player # or yarn add react-player
```

使用

```react
import React, {useEffect} from 'react'
import ReactPlayer from 'react-player'

const App = () => {
  
  useEffect(() => {
    ReactPlayer.canPlay(url)
  },[url])

  return <ReactPlayer url={url} light="xxx.jpg" onReady={} onError={} />
}
```

https://cookpete.com/react-player/





### react-motion



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



