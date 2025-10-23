---
title: React（二）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: http://cdn.kunkunzhang.top/react.png
---

​      前端框架，快速开发页面，函数式编程，与后端api快速搭建

<!--more-->

## 组件

react会将以小写字母开头的组件视为原生DOM标签，而组件名称必须以大写字母开口

组件的定义方式

以函数方式定义组件

```jsx
function Welcome(props){
    return <h1>hello,{props.name}</h1>
}
```

使用ES6的语法class定义组件

```jsx
class Welcome extends React.component{
    render(){
        return <h1>hello,{props.name}</h1>;
    }
}
```

引用组件

组件可以在输出中引用其他组件。在React中通常会以组件的形式表示。

组件被调用时可以携带参数，称为props，

```jsx
function Welcome(props){
    return <h1>hello,{props.name}</h1>
}

function App(){
    return (
       <div>
            <Welcome name="Sara" />
            <Welcome name="Cahs" />
            <Welcome name="hara" />
        </div>
    )
}

ReactDOM.render(
    <App />
    document.getElementById('root')
)
```

### 组件APi

在React中，组件以函数声明或者以Class方式声明。以Class方式声明时通常需要从React.Compoenent中继承。

React.Compoenent提供了生命周期api，因为生命周期的使用方式比较重要，这将在后文中介绍，这里首先介绍除了生命周期之外的其他API。

forceupdate：component.forceUpdate(callback)

默认情况下，当组件的 state 或 props 发生变化时，组件将重新渲染。如果 `render()` 方法依赖于其他数据，则可以调用 `forceUpdate()` 强制让组件重新渲染。

调用 `forceUpdate()` 将致使组件调用 `render()` 方法，此操作会跳过该组件的 `shouldComponentUpdate()`。但其子组件会触发正常的生命周期方法，包括 `shouldComponentUpdate()` 方法。如果标记发生变化，React 仍将只更新 DOM。

通常你应该避免使用 `forceUpdate()`，尽量在 `render()` 中使用 `this.props` 和 `this.state`。

错误处理api

static getDerivedStateFromError(error)

此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state

componentDidCatch(error, info)

此生命周期在后代组件抛出错误后被调用。 它接收两个参数：

1. `error` —— 抛出的错误。
2. `info` —— 带有 `componentStack` key 的对象，

`componentDidCatch()` 会在“提交”阶段被调用，因此允许执行副作用。 它应该用于记录错误之类的情况：

```react
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // "组件堆栈" 例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }
  
  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级  UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

如果发生错误，你可以通过调用 `setState` 使用 `componentDidCatch()` 渲染降级 UI，但在未来的版本中将不推荐这样做。 可以使用静态 `getDerivedStateFromError()` 来处理降级渲染。

### 受控组件与非受控组件

#### 派生state

大部分使用派生 state 导致的问题，不外乎两个原因：1，直接复制 props 到 state 上；2，如果 props 和 state 不一致就更新 state

受控和非受控

名词[“受控”](https://zh-hans.reactjs.org/docs/forms.html#controlled-components)和[“非受控”](https://zh-hans.reactjs.org/docs/uncontrolled-components.html)通常用来指代表单的 inputs，但是也可以用来描述数据频繁更新的组件。用 props 传入数据的话，组件可以被认为是**受控**（因为组件被父级传入的 props 控制）。数据只保存在组件内部的 state 的话，是**非受控**组件（因为外部没办法直接控制 state）。

当一个派生 state 值也被 `setState` 方法更新时，这个值就不是一个单一来源的值了。

如果组件的状态只能由用户控制，那么就是非受控组件，如果组件的状态可以由用户和通过代码两种方式控制，那么就是受控组件

在React中没有类似于Vue中v-model的双向绑定功能。

```react
class TestComponent extends React.Component {
  constructor (props){
    super(props);
    this.state = {username: 'lindaidai' }
  }
  render () {
		return <input name="username" value={this.state.username} />
  }
}
```

受控组件的完整定义：

在Html的表单元素中，它们通常自己维护一套state，并随着用户的数据自己进行UI上的更新，这种行为不被我们程序所控制。而如果将React的state属性和表单元素的值建立依赖关系，再通过onChange事件与setState()结合更新state属性，就能达到控制用户输入过程中表单发生的操作，被react以这种方式控制取值的表单输入元素就是受控组件

```react
class TestComponent extends React.Component {
  constructor (props){
    super(props);
    this.state = {
      username: 'lindaidai' 
    }
  }
  onChange (e){
    this.setState({
      username: e.target.value
    })
  }
  render () {
		return <input name="username" value={this.state.username} 
             onChange={(e)=> this.onChange(e)} />
  }
}
```

#### 受控组件注意事项

受控组件中有时会有派生state，即state的状态是根据props的值来进行变化

不建议将props直接复制到state中

最常见的误解就是 `getDerivedStateFromProps` 和 `componentWillReceiveProps` 只会在 props “改变”时才会调用。实际上只要父级重新渲染时，这两个生命周期函数就会重新调用，不管 props 有没有“变化”。所以，在这两个方法内直接复制（*unconditionally*）props 到 state 是不安全的。**这样做会导致 state 后没有正确渲染**。

错误使用1：在componentWillReceiveProps中直接使用prop初始化state

```react
class EmailInput extends Component {
  state = { email: this.props.email };

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />;
  }

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  componentWillReceiveProps(nextProps) {
    // 这会覆盖所有组件内的 state 更新！
    // 不要这样做。
    this.setState({ email: nextProps.email });
  }
}
```

乍看之下还可以。 state 的初始值是 props 传来的，当在 `<input>` 里输入时，修改 state。但是如果父组件重新渲染，我们输入的所有东西都会丢失！([查看这个示例](https://codesandbox.io/s/m3w9zn1z8x))，即使在重置 state 前比较 `nextProps.email !== this.state.email` 仍然会导致更新。

这个小例子中，使用 `shouldComponentUpdate` ，比较 props 的 email 是不是修改再决定要不要重新渲染。但是在实践中，一个组件会接收多个 prop，任何一个 prop 的改变都会导致重新渲染和不正确的状态重置。加上行内函数和对象 prop，创建一个完全可靠的 `shouldComponentUpdate` 会变得越来越难。[这个示例展示了这个情况](https://codesandbox.io/s/jl0w6r9w59)。而且 `shouldComponentUpdate` 的最佳实践是用于性能提升，而不是改正不合适的派生 state。

错误使用1：在componentWillReceiveProps中比较前后两次props再初始化state

```react
class EmailInput extends Component {
  state = {
    email: this.props.email
  };

  componentWillReceiveProps(nextProps) {
    // 只要 props.email 改变，就改变 state
    if (nextProps.email !== this.props.email) {
      this.setState({
        email: nextProps.email
      });
    }
  }
  
  // ...
}
```

现在组件只会在 prop 改变时才会改变。

但是仍然有个问题。想象一下，如果这是一个密码输入组件，拥有同样 email 的两个账户进行切换时，这个输入框不会重置（用来让用户重新登录）。因为父组件传来的 prop 值没有变化！这会让用户非常惊讶，因为这看起来像是帮助一个用户分享了另外一个用户的密码，

建议1:把组件包装成完全可控的组件

```react
function EmailInput(props) {
  return <input onChange={props.onChange} value={props.email} />;
}
```

建议2：为了在不同的页面切换不同的值，我们可以使用 `key` 这个特殊的 React 属性。当 `key` 变化时， React 会[创建一个新的而不是更新一个既有的组件](https://zh-hans.reactjs.org/docs/reconciliation.html#keys)。 Keys 一般用来渲染动态列表，但是这里也可以使用。

每次 ID 更改，都会重新创建 `EmailInput` ，并将其状态重置为最新的 `defaultEmail` 值。([点击查看这个模式的演示](https://codesandbox.io/s/6v1znlxyxn)) 使用此方法，不用为每次输入都添加 `key`，在整个表单上添加 `key` 更有位合理。每次 key 变化，表单里的所有组件都会用新的初始值重新创建。

```react
class EmailInput extends Component {
  state = { email: this.props.defaultEmail };

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  render() {
    return <input onChange={this.handleChange} value={this.state.email} />;
  }
}

<EmailInput
  defaultEmail={this.props.user.email}
  key={this.props.user.id}
/>
```

https://zh-hans.reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html

#### 封装组件为受控组件和非受控组件两种





### 组件间通信

父组件向子组件通讯: 父组件可以向子组件通过传 props 的方式，向子组件进行通讯

子组件向父组件通讯: props+回调的方式,父组件向子组件传递props进行通讯，此props为作用域为父组件自身的函数，子组件调用该函数，将子组件想要传递的信息，作为参数，传递到父组件的作用域中

兄弟组件通信: 找到这两个兄弟节点共同的父节点,结合上面两种方式由父节点转发信息进行通信

```jsx
import React from "react";

function Child1(props) {
  return (
    <div className="child">
      <p>{`兄弟1接收到的文本：${props.fatherText}`}</p>
    </div>
  );
}

class Child2 extends React.Component {
  state = { text: "兄弟2文本" };

  //调用了父组件传入的 changeFatherText 方法
  changeText = () => {
    this.props.changeFatherText(this.state.text);
  };

  render() {
    return (
      <div className="child">
        <button onClick={this.changeText}>点击更新兄弟1文本为兄弟2文本</button>
      </div>
    );
  }
}

export default class Father extends React.Component {
  // 初始化父组件的 state
  state = {
    text: "父组件的文本"
  };

  // 传给 Child2 组件按钮的监听函数，用于更新父组件 text 值（这个 text 值同时也是 Child1 的 props）
  changeText = (newText) => {
    this.setState({ text: newText });
  };

  // 渲染父组件
  render() {
    return (
      <div className="father">
        {/* 引入 Child1 组件，并通过 props 中下发具体的状态值 实现父-子通信 */}
        <Child1 fatherText={this.state.text} />

        {/* 引入 Child2 组件，并通过 props 中下发可传参的函数 实现子-父通信 */}
        <Child2 changeFatherText={this.changeText} />
      </div>
    );
  }
}
```

跨层级通信: `Context`设计目的是为了共享那些对于一个组件树而言是“全局”的数据，例如当前认证的用户、主题或首选语言,对于跨越多层的全局数据通过`Context`通信再适合不过

全局状态管理工具: 借助Redux或者Mobx等全局状态管理工具进行通信,这种工具会维护一个全局状态中心Store,并根据不同的事件产生新的状态

### context api

组件间层层嵌套时，传props的过程中会产生大量的...props或者propName={this.props.propValue}，导致代码异常丑陋，比如

```react
<App>
   <Switcher toggleState = {this.state.toggle}>
       <Pannel toggleState = {props.toggleState}>
           <div onClick={handleClick}>
             {props.toggleState?'1':'0'}
         	 </div>
     		</Pannel>
  </Switcher>
</App>
```

引入context api代码

简易版,通过provide的value传值，通过consumer的props接收值

```react
import React,{createContext} from 'react'

const {Provider,Consumer} = createContext('color');

class DeliverComponent extends React.component{
  state = {
    color:'orange',
    handleClick:() =>{
      this.setState({ color:'red'})
    }
  }
  render(){
    return (
      <Provider value= {this.state}>
         <MidComponent/>
      </Provider>
    )
  }
}

const MidComponent = () => <Receiver />

const Receiver = () =>(
    <Consumer>
      {({color,handleClick}) =>
  		<div style ={{color}} onClick={()=>{handleClick()}}>
       hello,world
      </div>}
    </Consumer>
)

const App =()=> <DeliverComponent/>

export default App;
```

复杂版

引入context api，创建provider和consumer

```react
//togglecontext.js
import React,{createContext} from 'react'
//创建上下文
const ToggleContext = createContext({
  toggle:true,
  handleToggle:()=>{}
})

//创建provider
export class ToggleProvider extends React.component{
  state = {
    toggle:true,
    handleToggle:this.handleToggle
  }

  render() {
    return (
      <ToggleContext.Provider value={this.state}>
        {this.props.children}
      </ToggleContext.Provider>
    )
  }
}
//创建consumer
export const ToggleConsumer = ToggleContext.Consumer
```

通过provider包裹组件传递value值可以使组件共享provider中的state，通过consumer获取props进行渲染

```react
import React from 'react';
import {ToggleProvider,ToggleConsumer} from './ToggleContext'

function App(){
  return (
    <ToggleProvider>
       <Switcher></Switcher>
    </ToggleProvider>
  )
}

const Switcher = () =>{
  return <Pannel/>
}

const Pannel = () =>{
  return (
    <ToggleConsumer>
      {({toggle.handleToggle})=>
         <div onClick={()=>handleToggle()}>
         {toggle?'1':'0'}
    		</div>
      }
    </ToggleConsumer>
  )
}

export default App
```

### ref、OnRef与forwardRef

在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件/元素

适合使用 refs 的情况：

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

ref 的值根据节点的类型而有所不同：

- 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
- 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
- 默认情况下，你不能在函数组件上使用 ref 属性（可以在函数组件内部使用），因为它们没有实例：
  - 如果要在函数组件中使用 ref，你可以使用 forwardRef（可与 useImperativeHandle 结合使用）
  - 或者可以将该组件转化为 class 组件。

父组件通过ref可以拿到子组件的方法和属性

```react
import React, { Component, Fragment } from "react";
import UncontrolledEmailInput from "./UncontrolledEmailInput";

export default class AccountsList extends Component {
  inputRef = React.createRef();

  state = {
    selectedIndex: 0
  };

  handleChange = index => {
    this.setState({ selectedIndex: index }, () => {
      const selectedAccount = this.props.accounts[index];
      this.inputRef.current.resetEmailForNewUser(selectedAccount.email);
    });
  };
render() {
    const { accounts } = this.props;
    const { selectedIndex } = this.state;
    const selectedAccount = accounts[selectedIndex];
    return (
      <Fragment>
        <h1>
          This demo illustrates resetting an uncontrolled component with an
          instance method
        </h1>
        <blockquote>First, make an edit to the account "One" email.</blockquote>
        <UncontrolledEmailInput
          defaultEmail={selectedAccount.email}
          ref={this.inputRef}
        />
        <blockquote>Next, select account "Two" below.</blockquote>
        <p>
          Accounts:
          {this.props.accounts.map((account, index) => (
            <label key={account.id}>
              <input
                type="radio"
                name="account"
                checked={selectedIndex === index}
                onChange={() => this.handleChange(index)}
              />{" "}
              {account.name}
            </label>
          ))}
        </p>
        <p>
          Even though both accounts have the same "committed" email, toggling
          between the two properly resets the "draft" email state. Read the
          inline comments in <code>UncontrolledEmailInput.js</code> to learn
          why.
        </p>
      </Fragment>
    );
  }
}
/// 子组件
import React, { Component } from "react";

// This is an example of an "uncontrolled" component.
// We call it this because the component manages its own "draft" state.
export default class UncontrolledEmailInput extends Component {
  // Default the "draft" email to the value passed in via props.
  state = {
    email: this.props.defaultEmail
  };

  // Imperative method to reset "draft" email state.
  // Call this method using a component ref.
  resetEmailForNewUser(defaultEmail) {
    this.setState({ email: defaultEmail });
  }

  handleChange = event => {
    this.setState({ email: event.target.value });
  };

  render() {
    return (
      <label>
        Email: <input onChange={this.handleChange} value={this.state.email} />
      </label>
    );
  }
}
```

Onref通过props将子组件的组件实例当作参数，通过回调传到父组件，然后在父组件就可以拿到子组件的实例了，拿到实例就可以调用它的方法了

```react
import Son from './son'

class Father extends React.Component {
  sonRef = (ref) => {
    this.child = ref
  }
  
  render() {
    return (
      <div>
         <Son onRef={this.sonRef}/>
      </div>
    )
  }
}
```

ref可以直接获得dom信息,非受控组件可以采用这种方式获取值而不进行其他操作

```react
import React,{ Component } from 'react'

export class UnControl extends Component {
  constructor (props) {
		super(props);
    this.inputRef = React.createRef();
  }
  handleSubmit = (e) => {
    e.preventDefault();
    console.log('input内的值为',this.inputRef.current.value);
  }
  render () {
    return (
    	<form onSubmit={e => this.handleSubmit(e)}>
        <input defaultValue="lindaidai" ref={this.inputRef}/>
        <input type="submit" value="提交"/>
      </form>
    )
  }
}
```

forwardRef多用于Ref 转发。Ref 转发是一项将 [ref](https://zh-hans.reactjs.org/docs/refs-and-the-dom.html) 自动地通过组件传递到其一子组件的技巧。对于大多数应用中的组件来说，这通常不是必需的。通常不建议这样做，因为它会打破组件的封装，但它偶尔可用于触发焦点或测量子 DOM 节点的大小或位置。但其对某些组件，尤其是可重用的组件库是很有用的。

没有使用`forwardRef`时，父组件传入子组件`ref`属性，此时`ref`指向的是**子组件本身**。但是如果想让`child`指向的是`Child`的`button`呢？此时在子组件中新建一个buttonRef，并作为拓展的`props`由父组件控制，新增一个字段如`buttonRef`。所以 React 提供了 `forwardRef`，用于将 ref 转发。这样子组件在**提供内部的 dom 时，不用扩充额外的 ref 字段**

Ref 转发是一个可选特性，其允许某些组件接收 `ref`，并将其向下传递（换句话说，“转发”它）给子组件。

```react
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

转发ref在父组件作为别的组件的子组件时会比较方便, 也就是HOC

```react
import Button from './Button';
const LoggedButton = logProps(Button);

const ref = React.createRef();

// LoggedButton 组件是高阶组件（HOC）LogProps。
// 尽管渲染结果将是一样的，
// 但我们的 ref 将指向 LogProps 而不是内部的 Button 组件！
// 这意味着我们不能调用例如 ref.current.xxx() 这样的方法
<LoggedButton label="Click Me" handleClick={handleClick} ref={ref} />;

function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

使用forwardRef和useImperativeHandle限制父组件调用子组件的Api

Button组件提供了`onChange`回调，**外部组件可以传入`onChange`方法获取实时的`status`，Button内部则通过`onToggleStatus`控制状态**。

如果现在另一个开发人员开发外部组件时，想要实现在外部实现第二个按钮**实时控制和同步显示Button的状态**。此时他已经可以通过`onChange`实时同步状态，而从外部修改Button状态则一般有两种方式：

1. 修改Button组件为纯函数组件，将其状态和修改状态的方法提升至父组件或者状态管理工具中。
2. 通过ref拿到该组件，通过`ref.current.onToggleStatus()`的方式修改子组件状态。



#### ref的其他用法

16.3之前可以通过字符或者回调函数两个方式获取ref

```react
// string ref
class MyComponent extends React.Component {
  componentDidMount() {
    this.refs.myRef.focus();
  }

  render() {
    return <input ref="myRef" />;
  }
}

// callback ref
class MyComponent extends React.Component {
  componentDidMount() {
    this.myRef.focus();
  }

  render() {
    return <input ref={(ele) => {
      this.myRef = ele;
    }} />;
  }
}
```

string ref 就已被诟病已久，React 官方文档中如此声明：`"如果你目前还在使用 this.refs.textInput 这种方式访问 refs ，我们建议用回调函数或 createRef API 的方式代替。"`

吐槽内容主要有以下几点:

1. string ref 不可组合。 例如一个第三方库的父组件已经给子组件传递了 ref，那么我们就无法在在子组件上添加 ref 了。 另一方面，回调引用没有一个所有者，因此您可以随时编写它们。

2. string ref 的所有者由当前执行的组件确定。 这意味着使用通用的“渲染回调”模式（例如react），错误的组件将拥有引用（它将最终在react上而不是您的组件定义renderRow）
3. string ref 不适用于Flow之类的静态分析。 Flow不能猜测框架可以使字符串ref“出现”在react上的神奇效果，以及它的类型（可能有所不同）。 回调引用比静态分析更友好。
4. string ref 强制React跟踪当前正在执行的组件。 这是有问题的，因为它使react模块处于有状态，并在捆绑中复制react模块时导致奇怪的错误。在 reconciliation 阶段，React Element 创建和更新的过程中，ref 会被封装为一个闭包函数，等待 commit 阶段被执行，这会对 React 的性能产生一些影响。

而callback ref则一直可以。React 将在组件挂载时，会调用 ref 回调函数并传入 DOM 元素，当卸载时调用它并传入 null。在 componentDidMount 或 componentDidUpdate 触发前，React 会保证 refs 一定是最新的。

如果 ref 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 null，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

16.3之后class组件中有了createRef，相比于之前的ref使用方式，优点：

- 相对于 callback ref 而言 React.createRef 显得更加直观，避免了 callback ref 的一些理解问题。

React.createRef 的缺点：

1. 性能略低于 callback ref
2. 能力上仍逊色于 callback ref，例如上一节提到的组合问题，createRef 也是无能为力的。



### 列表组件

使用key时，不能使用数组的index作为列表组件的key

使用index作为key的列表，向列表中添加或删除某些项时可能导致错误的显示。因为key是连接真实DOM的标识，当更改后的key与更改前的key相同时，react会认为前后的组件是相同的，但其实这两项并不一样

### Constructor

class组件中有constructor构造函数，有两个目的

1.初始化this.state

2.函数方法绑定到实例

```react
constructor(props) {
  super(props);
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this)
}
```

使用箭头函数则不需要将事件在constructor中绑定

### props默认值

对于函数组件，设置函数的defaultprops属性

```react
 import React from 'react'

 function About (props) {
   const { name, age } = props
     return (
       <div>
         <p>{ name }</p>
         <p>{ age }</p>
       </div>
     )
 }

 About.defaultProps = {
   name: 'ReoNa',
   age: 22
 }
 
 export default About
```

对于类组件，我们直接定义 `static defaultProps`设置默认值

```react
 import React, { Component } from 'react'
 
 class Header extends Component {
 
   static defaultProps = {
     name: 'Aimyon',
     age: 25
   }
 
   render () {
     const { name, age } = this.props
     return (
       <div>
         <p>{ name }</p>
         <p>{ age }</p>
       </div>
     )
   }
 }
 
 export default Header
```

#### 子组件修改props的方法

父组件使用ref

父组件对子组件传入改变props的方法，由自组件调用

#### 父组件调用子组件的方法

1.父组件中使用Ref创建回调

缺点：如果子组件是嵌套了HOC，就无法指向真实子组件

创建方式有两种，类组件中使用React.createRef, 也可以使用ref回调函数的方式

```react
import React , { Component } from "react"

// ---------- 子组件 ----------
class Child extends Component<any, any> {
  func(){
    console.log("执行我")
  }
  render(){
    return (<div>子组件</div>);
  }
}

// ---------- 父组件 ----------
class Parent extends Component<any, any> {
  // ChildRef = React.createRef<any>();
  ChildRef: any = null
  handleOnClick(){
    this.ChildRef?.func?.();
  }
  render(){
    return (<div>
              <button onClick={this.handleOnClick}>click</button>
             /**  <Child ref={this.ChildRef} /> */
	      <Child ref={ node => this.ChildRef = node }/>
	    </div>);
  }
}
```

2.自定义props属性传递函数

```react
import React , { Component } from "react"

// ---------- 子组件 ----------
type ChildProps = {
  onRef?: (node: any) => void;
}
class Child extends Component<ChildProps, any> {
  componentDidMount(){
    this.props?.onRef?.(this);
  }
  func(){
    console.log("执行我")
  }
  render(){
    return (<div>子组件</div>);
  }
}

// ---------- 父组件 ----------
class Parent extends Component<any, any> {
  ChildRef: any = null
  handleOnClick(){
    this.Child?.func?.();
  }
  render(){
    return (<div>
	      <button onClick={this.handleOnClick}>click</button>
	      <Child onRef={ node => this.ChildRef = node } />
	    </div>);
  }
}
```

3.hooks

```react
import React, { useImperativeHandle } from 'react';

// ---------- 子组件 ----------
type ChildProps = {
  onRef?: any;
}
const Child: React.FC<ChildProps> = props => {
  //用useImperativeHandle暴露一些外部ref能访问的属性
  useImperativeHandle(props.onRef, () => {
    return {
      func: func,
    };
  });
  const func = () => {
    console.log('执行我');
  }
  return <div>子组件</div>;
};

// ---------- 父组件 ----------
const Parent: React.FC = () => {
  // 以下两种写法均正确
  // const ChildRef = React.createRef();
  const ChildRef: any = useRef();

  function handleOnClick() {
    ChildRef.current?.func?.();
  }

  return (
    <div>
      <button onClick={handleOnClick}>click</button>
      <Child onRef={ChildRef} />
    </div>
  );
};
```

4.forwardRef

```react
import React from 'react';

// ---------- 子组件 ----------
// 自定义可以抛出子组件ref的HOC
const withChild: (Comp: any) => any = Comp => {
  return React.forwardRef((props, ref) => {
    return <Comp ref={ref} {...props} />;
  });
}

class Child extends Component {
  func = () => {
    console.log('打印了我');
  };
  render() {
    return <div>我是个测试的子组件</div>;
  }
}

const ChildWrapper = withChild(Child);

// ---------- 父组件 ----------
const Parent = () => {
  // 以下两种写法均正确
  // const ChildRef = React.createRef();
  const ChildRef: any = useRef();

  function handleOnClick() {
    ChildRef.current?.func?.();
  }

  return (
    <div>
      <button onClick={handleOnClick}>click</button>
      <Child ref={ChildRef} />
    </div>
  );
};
```



### 高级：正交组件

如果A和B正交的，则更改A不会更改B（反之亦然）。这就是正交性的概念。在广播设备中，音量和电台选择控件是正交的。音量控制仅更改音量，而电台选择控件仅更改接收到的电台

一个好的React应用程序设计是正交的：

- UI元素
- 全局状态管理
- 持久性逻辑（本地存储，cookie）
- 获取数据 （fetch library, REST or GraphQL）

将组件隔离，并独立封装。这将使你的组件正交，并且你所做的任何更改都将被隔离，并且仅集中在一个组件上。这就是可预测且易于开发的系统的诀窍

- 使用React hooks？它们使**UI渲染逻辑**与**state**和**副作用逻辑**正交
- 为什么Suspense获取？它使获取的细节和组件正交

正交组件的好处：

易于修改：当组件是正交设计时，对组件所做的任何更改都将隔离在组件内。

易读：由于正交组件仅负责一个任务，因此更容易了解该组件的功能，它不被不属于这里的细节所困扰。

易测试：正交组件仅专注于执行单个任务，你要做的只是测试组件是否正确执行任务。通常，非正交组件需要大量的模拟和手动设置才能进行测试，而且，如果难以测试。而现在你只需修改单个组件。

### UI组件、业务组件与增强组件

纯UI组件是指组件中没有或者只有较少逻辑且完全受控的组件

业务组件与增强组件：

业务组件中一般会写一些与业务强相关的接口/逻辑，这些逻辑在别的系统就不可以使用了，所以称为业务组件

增强组件是一个增强功能的组件，组件中没有单独的逻辑，基本上props是一些通用的api或者数据。

#### 为什么函数式组件必须引入React

react的函数式组件中必须引入React，比如像这样

```javascript
import React from "react";
const App = () => (
  <div>Hello World!!!</div>
);
export default App;
```

原因是Babel在转译app.js时会把jsx语法糖转换为React.createElement方法

```javascript
var App = function App() {
  return React.createElement(
    "div",
    null,
    "Hello World!!!"
  );
};
```

那能不能直接写函数组件，而不需要在组件顶部引入React组件呢

可以。通过babel的插件babel-plugin-react-require 自动分别无状态组件，如果是则自动引入react

安装

```shell
npm install babel-plugin-react-require --save-dev
```

在 `.babelrc` 加入 `react-require`

```json
{
  "plugins": [
    "react-require"
  ]
}
```

也可以修改插件，使得编译后的代码生成自己的虚拟Dom函数。比如deku等

https://juejin.cn/post/6844903783655276557

### react哲学

在react官网上看到一篇很好的博客，摘要一些东西放这里

React 是用 JavaScript 构建快速响应的大型 Web 应用程序的首选方式。它在 Facebook 和 Instagram 上表现优秀

React 最棒的部分之一是引导我们思考如何构建一个应用。

假设我们已经有了一个返回 JSON 的 API，以及设计师提供的组件设计稿，应该如何设计代码/组件呢

第一步，将设计好的UI划分组件层级

首先，你需要在设计稿上用方框圈出每一个组件（包括它们的子组件），并且以合适的名称命名。如果你是和设计师一起完成此任务，那么他们可能已经做过类似的工作，所以请和他们进行交流！他们的 Photoshop 的图层名称可能最终就是你编写的 React 组件的名称！

但你如何确定应该将哪些部分划分到一个组件中呢？你可以将组件当作一种函数或者是对象来考虑，根据[单一功能原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)来判定组件的范围。也就是说，一个组件原则上只能负责一个功能。如果它需要负责更多的功能，这时候就应该考虑将它拆分成更小的组件

在实践中，因为你经常是在向用户展示 JSON 数据模型，所以如果你的模型设计得恰当，UI（或者说组件结构）便会与数据模型一一对应，这是因为 UI 和数据模型都会倾向于遵守相同的*信息结构*。将 UI 分离为组件，其中每个组件需与数据模型的某部分匹配

现在我们已经确定了设计稿中应该包含的组件，接下来我们将把它们描述为更加清晰的层级。设计稿中被其他组件包含的子组件，在层级上应该作为其子节点

第二步，用React创建一个 静态版本

现在我们已经确定了组件层级，可以编写对应的应用了。最容易的方式，是先用已有的数据模型渲染一个不包含交互功能的 UI。最好将渲染 UI 和添加交互这两个过程分开。这是因为，编写一个应用的静态版本时，往往要编写大量代码，而不需要考虑太多交互细节；添加交互功能时则要考虑大量细节，而不需要编写太多代码。所以，将这两个过程分开进行更为合适。

在构建应用的静态版本时，我们需要创建一些会重用其他组件的组件，然后通过 *props* 传入所需的数据。*props* 是父组件向子组件传递数据的方式。即使你已经熟悉了 *state* 的概念，也**完全不应该使用 state** 构建静态版本。state 代表了随时间会产生变化的数据，应当仅在实现交互时使用。所以构建应用的静态版本时，你不会用到它

你可以自上而下或者自下而上构建应用：自上而下意味着首先编写层级较高的组件（比如 `FilterableProductTable`），自下而上意味着从最基本的组件开始编写（比如 `ProductRow`）。当你的应用比较简单时，使用自上而下的方式更方便；对于较为大型的项目来说，自下而上地构建，并同时为低层组件编写测试是更加简单的方式

到此为止，你应该已经有了一个可重用的组件库来渲染你的数据模型。由于我们构建的是静态版本，所以这些组件目前只需提供 `render()` 方法用于渲染。最顶层的组件 `FilterableProductTable` 通过 props 接受你的数据模型。如果你的数据模型发生了改变，再次调用 `root.render()`，UI 就会相应地被更新。数据模型变化、调用 `render()` 方法、UI 相应变化，这个过程并不复杂，因此很容易看清楚 UI 是如何被更新的，以及是在哪里被更新的。React **单向数据流**（也叫*单向绑定*）的思想使得组件模块化，易于快速开发

第三步，确定UI state的最小表示

想要使你的 UI 具备交互功能，需要有触发基础数据模型改变的能力。React 通过实现 **state** 来完成这个任务

为了正确地构建应用，你首先需要找出应用所需的 state 的最小表示，并根据需要计算出其他所有数据。其中的关键正是 [DRY: *Don’t Repeat Yourself*](https://en.wikipedia.org/wiki/Don't_repeat_yourself)。只保留应用所需的可变 state 的最小集合，其他数据均由它们计算产生。比如，你要编写一个任务清单应用，你只需要保存一个包含所有事项的数组，而无需额外保存一个单独的 state 变量（用于存储任务个数）。当你需要展示任务个数时，只需要利用该数组的 length 属性即可

通过问自己以下三个问题，你可以逐个检查相应数据是否属于 state：

1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state

第四步：确定state放置的位置

我们已经确定了应用所需的 state 的最小集合。接下来，我们需要确定哪个组件能够改变这些 state，或者说*拥有*这些 state。

注意：React 中的数据流是单向的，并顺着组件层级从上往下传递。哪个组件应该拥有某个 state 这件事，**对初学者来说往往是最难理解的部分**。尽管这可能在一开始不是那么清晰，但你可以尝试通过以下步骤来判断：

对于应用中的每一个 state：

- 找到根据这个 state 进行渲染的所有组件。
- 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
- 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
- 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。

第五步：添加反向数据流

到目前为止，我们已经借助自上而下传递的 props 和 state 渲染了一个应用。现在，我们将尝试让数据反向传递：处于较低层级的表单组件更新较高层级的 `FilterableProductTable` 中的 state。

React 通过一种比传统的双向绑定略微繁琐的方法来实现反向数据传递。尽管如此，但这种需要显式声明的方法更有助于人们理解程序的运作方式。

如果你尝试在上一个示例的搜索框中输入或勾选复选框（步骤 4），React 不会产生任何响应。这是正常的，因为我们之前已经将 `input` 的值设置为了从 `FilterableProductTable` 的 `state` 传递而来的固定值。

让我们重新梳理一下需要实现的功能：每当用户改变表单的值，我们需要改变 state 来反映用户的当前输入。由于 state 只能由拥有它们的组件进行更改，`FilterableProductTable` 必须将一个能够触发 state 改变的回调函数（callback）传递给 `SearchBar`。我们可以使用输入框的 `onChange` 事件来监视用户输入的变化，并通知 `FilterableProductTable` 传递给 `SearchBar` 的回调函数。然后该回调函数将调用 `setState()`，从而更新应用

https://zh-hans.reactjs.org/docs/thinking-in-react.html



### 进阶：构建组件的哲学

#### 自上而下地设计组件

自上而下的设计组件通常能更直接地设计组件，但是要避免设计出巨大的单体组件。与之相对的是自下而上地设计组件。

巨大的单体组件意味着难以组合和抽象，而且会让组件变得臃肿和变得有风险。

https://frontendmastery.com/posts/building-future-facing-frontend-architectures/

#### 使用多组件共同完成任务

可以用一个稳定性较高的组件包裹一些较小的组件，好过传递全部props进一个巨大的单体组件

```javascript
export const Tab = ({ children }) => {
  const tabAttributes = useTab()
  return (
    <div {...tabAttributes}>
      {children}
    </div>
  )
}

export const TabPanel = ({ children }) => {
  const tabPanelAttributes = useTabPanel()
  return (
    <div {...tabPanelAttributes}>
      {children}
    </div>
  )
}
```

这其中可以使用context传递数据

```javascript
export const TabsList = ({ children }) => {
  // provided by top level Tabs component coming up next
  const { tabsId, currentTabIndex, onTabChange } = useTabList()
  // store a reference to the DOM element so we can select via id
  // and manage the focus states 
  const ref = createRef()
  
  const selectTabByIndex = (index) => {
    const selectedTab = ref.current.querySelector(
      `[id=${tabsId}-${index}]`
    )
    selectedTab.focus()
    onTabChange(index)
  }
  // we would handle keyboard events here 
  // things like selecting with left and right arrow keys
  const onKeyDown = () => {
   // ...
  }
  // .. some other stuff - again we're omitting styles etc
  return (
    <div role="tablist" ref={ref}>
      {React.Children.map(children, (child, index) => {
          const isSelected = index === currentTabIndex
          return (
            <TabContext.Provider
              // (!) in real life this would need to be restructured 
              // (!) and memoized to use a stable references everywhere
              value={{
                key: `${tabsId}-${index}`,
                id: `${tabsId}-${index}`,
                role: 'tab',
                'aria-setsize': length,
                'aria-posinset': index + 1,
                'aria-selected': isSelected,
                'aria-controls': `${tabsId}-${index}-tab`,
                // managing focussability
                tabIndex: isSelected ? 0 : -1,
                onClick: () => selectTabByIndex(index),
                onKeyDown,
              }}
            >
              {child}
            </TabContext.Provider>
          )
        }
      )}
    </div>
  )
}
```

也可以多个Context、useState管理数据，像这样

```javascript
const TabContext = createContext(null)
const TabListContext = createContext(null)
const TabPanelContext = createContext(null)

export const useTab = () => {
  const tabData = useContext(TabContext)
  if (tabData == null) {
    throw Error('A Tab must have a TabList parent')
  }
  return tabData
}

export const useTabPanel = () => {
  const tabPanelData = useContext(TabPanelContext)
  if (tabPanelData == null) {
    throw Error('A TabPanel must have a Tabs parent')
  }
  return tabPanelData
}

export const useTabList = () => {
  const tabListData = useContext(TabListContext)
  if (tabListData == null) {
    throw Error('A TabList must have a Tabs parent')
  }
  return tabListData
}
```



#### 设计好组件的api/props的原则

1.不要把组件的setState传给子组件

最好使用调用函数的方式

2.不要在同一个props上绑定多个状态，造成难维护的组件。



#### 获取组件props

当我们在封装一个高阶组件时，常常需要用到原组件的props类型，然后添加上新的props。比如在react-native中，当需要使用Image组件，封装一个可以自动获取图片宽高的ResizeImage组件时，我们需要先获得Image组件的所有props的类型，然后使用联合类型创建一个ResizeImage的props类型

`React.ComponentProps<typeof Image>`，这样我们就可以愉快的创造一个新的props类型，而不丢失Image原有的props类型啦！ stackoverflow上还有这样一种解决方法：`type PropsOf<C> = C extends (props: infer P) => any ? P : never`

#### 拆分context

如果 Context value 中包含了多个 state，那么我们可以把它们按照相关性拆分成多个 Context，这样可以避免不必要的 re-render

```react
const Component = () => {
  const [data, setData] = useState();
  const [data1, setData1] = useState();
  return (
    <DataContext.Provider value={data}>
      <Data1Context.Provider value={data1}>
        <Child />
      </Data1Context.Provider>
    </DataContext.Provider>
  );
};
```

#### 实现类型selector的功能

如果我们想实现一个类似于 redux 的 useSelector 的功能，只有在 Context 中的某个值发生变化的时候，才会触发 re-render，我们可以通过高阶组件来实现

```react
const withSelector = (selector) => (Component) => {
  const MemoComponent = React.memo(Component);
  return (props) => {
    const value = useContext(selector.context);
    const selectedValue = selector.selector(value);
    return <MemoComponent {...props} {...selectedValue} />;
  };
};
```

withSelector(selector) 返回了一个 高阶组件，这个高阶组件在 selector.context 发生变化的时候，会触发 re-render，然后通过 selector.selector 来选择需要的值，然后传递给 Component 的 memo 版本，所以 Component 只在需要的值变化后，才会 re-render



## HOC与render Props

### 包装强化组件的方式

1.最早的mixin方式，已弃用

在`react`初期提供一种组合方法。通过`React.createClass`,加入`mixins`属性，具体用法和`vue` 中`mixins`相似

```react
const customMixin = {
  componentDidMount(){
    console.log( '------componentDidMount------' )
  },
  say(){
    console.log(this.state.name)
  }
}

const APP = React.createClass({
  mixins: [ customMixin ],
  getInitialState(){
    return {
      name:'alien'
    }
  },
  render(){
    const { name  } = this.state
    return <div> hello ,world , my name is { name } </div>
  }
})
```

这种`mixins`只能存在`createClass`中，后来`React.createClass`连同`mixins`这种模式被废弃了。`mixins`会带来一些负面的影响。

- 1 mixin引入了隐式依赖关系。
- 2 不同mixins之间可能会有先后顺序甚至代码冲突覆盖的问题
- 3 mixin代码会导致滚雪球式的复杂性

2.extends继承模式

在`class`组件盛行之后，我们可以通过继承的方式进一步的强化我们的组件。这种模式的好处在于，可以封装基础功能组件，然后根据需要去`extends`我们的基础组件，按需强化组件，但是值得注意的是，必须要对基础组件有足够的掌握，否则会造成一些列意想不到的情况发生

```react
class Base extends React.Component{
  constructor(){
    super()
    this.state={
      name:'alien'
    }
  }
  say(){
    console.log('base components')
  }
  render(){
    return <div> hello,world <button onClick={ this.say.bind(this) } >点击</button>  </div>
  }
}
class Index extends Base{
  componentDidMount(){
    console.log( this.state.name )
  }
  say(){ /* 会覆盖基类中的 say  */
    console.log('extends components')
  }
}
export default Index
```

3.HOC

```react
function HOC(Component) {
  return class wrapComponent extends React.Component{
     constructor(){
       super()
       this.state={
         name:'alien'
       }
     }
     render=()=><Component { ...this.props } { ...this.state } />
  }
}

@HOC
class Index extends React.Component{
  say(){
    const { name } = this.props
    console.log(name)
  }
  render(){
    return <div> hello,world <button onClick={ this.say.bind(this) } >点击</button>  </div>
  }
}
```



### HOC

HOC的基本原理可以写成这样：

```react
const HOCFactory = (Component) => {
  return class HOC extends React.Component {
    render(){
      return <Component {...this.props} />
    }
  }
}
```

HOC最大的特点就是：接受一个组件作为参数，返回一个新的组件。

组件是把`prop`渲染成`UI`,而高阶组件是将组件转换成另外一个组件

高阶组件解决的问题

**① 复用逻辑**：高阶组件更像是一个加工`react`组件的工厂，批量对原有组件进行**加工**，**包装**处理。我们可以根据业务需求定制化专属的`HOC`,这样可以解决复用逻辑。

**② 强化props**：这个是`HOC`最常用的用法之一，高阶组件返回的组件，可以劫持上一层传过来的`props`,然后混入新的`props`,来增强组件的功能。代表作`react-router`中的`withRouter`。

**③ 赋能组件**：`HOC`有一项独特的特性，就是可以给被`HOC`包裹的业务组件，提供一些拓展功能，比如说**额外的生命周期，额外的事件**，但是这种`HOC`，可能需要和业务组件紧密结合。典型案例`react-keepalive-router`中的 `keepaliveLifeCycle`就是通过`HOC`方式，给业务组件增加了额外的生命周期。

**④ 控制渲染**：劫持渲染是`hoc`一个特性，在`wrapComponent`包装组件中，可以对原来的组件，进行`条件渲染`，`节流渲染`，`懒加载`等功能，后面会详细讲解，典型代表做`react-redux`中`connect`和 `dva`中 `dynamic` 组件懒加载

HOC的优点：

- 支持ES6，光这一项就战胜了mixins
- 复用性强，HOC是纯函数且返回值仍为组件，在使用时可以多层嵌套，在不同情境下使用特定的HOC组合也方便调试。
- 同样由于HOC是纯函数，支持传入多个参数，增强了其适用范围。

HOC的缺点：

- 当有多个HOC一同使用时，无法直接判断子组件的props是哪个HOC负责传递的。
- 重复命名的问题：若父子组件有同样名称的props，或使用的多个HOC中存在相同名称的props，则存在覆盖问题，而且react并不会报错。当然可以通过规范命名空间的方式避免。
- 在react开发者工具中观察HOC返回的结构，可以发现HOC产生了许多无用的组件，加深了组件层级。
- 同时，HOC使用了静态构建，即当AppWithMouse被创建时，调用了一次withMouse中的静态构建。而在render中调用构建方法才是react所倡导的动态构建。与此同时，在render中构建可以更好的利用react的生命周期。

render prop 的出现解决了以上问题

#### 两种不同的HOC

常用的高阶组件有两种方式**正向的属性代理**和**反向的组件继承**，两者之前有一些共性和区别。

所谓正向属性代理，就是用组件包裹一层代理组件，在代理组件上，我们可以做一些，对源组件的代理操作。在`fiber tree` 上，先`mounted`代理组件，然后才是我们的业务组件。我们可以理解为父子组件关系，父组件对子组件进行一系列强化操作

```react
class Index extends React.Component{
  render(){
    return <div> hello,world  </div>
  }
}
Index.say = function(){
  console.log('my name is alien')
}
function HOC(Component) {
  return class wrapComponent extends React.Component{
     render(){
       return <Component { ...this.props } { ...this.state } />
     }
  }
}
const newIndex =  HOC(Index) 
console.log(newIndex.say)
```

优点：

① 正常属性代理可以和业务组件低耦合，零耦合，对于`条件渲染`和`props属性增强`,只负责控制子组件渲染和传递额外的`props`就可以，所以无须知道，业务组件做了些什么。所以正向属性代理，更适合做一些开源项目的`hoc`，目前开源的`HOC`基本都是通过这个模式实现的。

② 同样适用于`class`声明组件，和`function`声明的组件。

③ 可以完全隔离业务组件的渲染,相比反向继承，属性代理这种模式。可以完全控制业务组件渲染与否，可以避免`反向继承`带来一些副作用，比如生命周期的执行。

④ 可以嵌套使用，多个`hoc`是可以嵌套使用的，而且一般不会限制包装`HOC`的先后顺序。

缺点：

- ① 一般无法直接获取业务组件的状态，如果想要获取，需要`ref`获取组件实例。
- ② 无法直接继承静态属性。如果需要继承需要手动处理，或者引入第三方库。

反向继承和属性代理有一定的区别，在于包装后的组件继承了业务组件本身，所以我们我无须在去实例化我们的业务组件。当前高阶组件就是继承后，加强型的业务组件。这种方式类似于组件的强化

```react
class Index extends React.Component{
  render(){
    return <div> hello,world  </div>
  }
}
Index.say = function(){
  console.log('my name is alien')
}
function HOC(Component) {
  return class wrapComponent extends Component{
  }
}
const newIndex =  HOC(Index) 
console.log(newIndex.say)
```

优点：

- ① 方便获取组件内部状态，比如`state`，`props` ,生命周期,绑定的事件函数等
- ② `es6`继承可以良好继承静态属性。我们无须对静态属性和方法进行额外的处理。

缺点：

① 无状态组件无法使用。

② 和被包装的组件强耦合，需要知道被包装的组件的内部状态，具体是做什么？

③ 如果多个反向继承`hoc`嵌套在一起，当前状态会覆盖上一个状态。这样带来的隐患是非常大的，比如说有多个`componentDidMount`，当前`componentDidMount`会覆盖上一个`componentDidMount`。这样副作用串联起来，影响很大。

#### 强化props

这个是高阶组件最常用的功能，承接上层的`props`,在混入自己的`props`，来强化组件

强化props的案例是`withRoute`。`withRoute`用途就是，对于没有被`Route`包裹的组件，给添加`history`对象等和路由相关的状态，方便我们在任意组件中，都能够获取路由状态，进行路由跳转，这个`HOC`目的很清楚，就是强化`props`,把`Router`相关的状态都混入到`props`中

```react
function classHOC(WrapComponent){
    return class  Idex extends React.Component{
        state={
            name:'alien'
        }
        componentDidMount(){
           console.log('HOC')
        }
        render(){
            return <WrapComponent { ...this.props }  { ...this.state }   />
        }
    }
}
function Index(props){
  const { name } = props
  useEffect(()=>{
     console.log( 'index' )
  },[])
  return <div>
    hello,world , my name is { name }
  </div>
}

export default classHOC(Index)

function functionHoc(WrapComponent){
    return function Index(props){
        const [ state , setState ] = useState({ name :'alien'  })       
        return  <WrapComponent { ...props }  { ...state }   />
    }
}
```

高阶组件也可以将`HOC`的`state`的配合起来，控制业务组件的更新。这种用法在`react-redux`中`connect`高阶组件中用到过，用于处理来自`redux`中`state`更改，带来的订阅更新作用

```react
function classHOC(WrapComponent){
  return class  Idex extends React.Component{
      constructor(){
        super()
        this.state={
          name:'alien'
        }
      }
      changeName(name){
        this.setState({ name })
      }
      render(){
          return <WrapComponent { ...this.props }  { ...this.state } changeName={this.changeName.bind(this)  }  />
      }
  }
}
function Index(props){
  const [ value ,setValue ] = useState(null)
  const { name ,changeName } = props
  return <div>
    <div>   hello,world , my name is { name }</div>
    改变name <input onChange={ (e)=> setValue(e.target.value)  }  />
    <button onClick={ ()=>  changeName(value) }  >确定</button>
  </div>
}

export default classHOC(Index)
```

#### 控制渲染

控制渲染是高阶组件的一个很重要的特性

对于属性代理的高阶组件，虽然不能在内部操控渲染状态，但是可以在外层控制当前组件是否渲染，这种情况应用于，动态挂载、**权限隔离**，**懒加载** ，**延时加载**等场景

```react
// 动态挂载
function renderHOC(WrapComponent){
  return class Index  extends React.Component{
      constructor(props){
        super(props)
        this.state={ visible:true }  
      }
      setVisible(){
         this.setState({ visible:!this.state.visible })
      }
      render(){
         const {  visible } = this.state 
         return <div className="box"  >
           <button onClick={ this.setVisible.bind(this) } > 挂载组件 </button>
           { visible ? <WrapComponent { ...this.props } setVisible={ this.setVisible.bind(this) }   />  : <div className="icon" ><SyncOutlined spin  className="theicon"  /></div> }
         </div>
      }
  }
}

class Index extends React.Component{
  render(){
    const { setVisible } = this.props
    return <div className="box" >
        <p>hello,my name is alien</p>
        <img  src='https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=294206908,2427609994&fm=26&gp=0.jpg'   /> 
        <button onClick={() => setVisible()}  > 卸载当前组件 </button>
    </div>
  }
}
export default renderHOC(Index)

// 懒加载HOC
export default function AsyncRouter(loadRouter) {
  return class Content extends React.Component {
    state = {Component: null}
    componentDidMount() {
      if (this.state.Component) return
      loadRouter()
        .then(module => module.default)
        .then(Component => this.setState({Component},
         ))
    }
    render() {
      const {Component} = this.state
      return Component ? <Component {
      ...this.props
      }
      /> : null
    }
  }
}

const Index = AsyncRouter(()=>import('../pages/index'))
```

控制渲染比较典型的使用案例是connect。`connect`的作用也有`合并props`，但是更重要的是接受`state`，来控制更新组件。

#### 赋能组件

高阶组件还可以赋能组件，比如加一些**额外`生命周期`**，**劫持事件**，**监控日志**等等。

```react
// 组件内的事件监听
function ClickHoc (Component){
  return  function Wrap(props){
    const dom = useRef(null)
    useEffect(()=>{
     const handerClick = () => console.log('发生点击事件') 
     dom.current.addEventListener('click',handerClick)
     return () => dom.current.removeEventListener('click',handerClick)
    },[])
    return  <div ref={dom}  ><Component  {...props} /></div>
  }
}

@ClickHoc
class Index extends React.Component{
   render(){
     return <div  className='index'  >
       <p>hello，world</p>
       <button>组件内部点击</button>
    </div>
   }
}
export default ()=>{
  return <div className='box'  >
     <Index />
     <button>组件外部点击</button>
  </div>
}
```

开源库`react-keepalive-router`中使用`HOC` 组件`keepaliveLifeCycle` 包裹，缓存组件的生命周期

```react
import {lifeCycles} from '../core/keeper'
import hoistNonReactStatic from 'hoist-non-react-statics'
function keepaliveLifeCycle(Component) {
   class Hoc extends React.Component {
    cur = null
    handerLifeCycle = type => {
      if (!this.cur) return
      const lifeCycleFunc = this.cur[type]
      isFuntion(lifeCycleFunc) && lifeCycleFunc.call(this.cur)
    }
    componentDidMount() { 
      const {cacheId} = this.props
      cacheId && (lifeCycles[cacheId] = this.handerLifeCycle)
    }
    componentWillUnmount() {
      const {cacheId} = this.props
      delete lifeCycles[cacheId]
    }
     render=() => <Component {...this.props} ref={cur => (this.cur = cur)}/>
  }
  return hoistNonReactStatic(Hoc,Component)
}
```

https://juejin.cn/post/6940422320427106335#heading-24

### render-props

相比于直接将 `<Cat>` 写死在 `<Mouse>` 组件中，并且有效地更改渲染的结果，我们可以为 `<Mouse>` 提供一个函数 prop 来动态的确定要渲染什么 —— 一个 render prop

Render Props 的核心思想是，通过一个函数将class组件的state作为props传递给纯函数组件

具有 render prop 的组件接受一个返回 React 元素的函数，并在组件内部通过调用此函数来实现自己的渲染逻辑

render prop 是因为模式才被称为 *render* prop ，你不一定要用名为 `render` 的 prop 来使用这种模式。事实上， [*任何*被用于告知组件需要渲染什么内容的函数 prop 在技术上都可以被称为 “render prop”](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce).

`children` prop 并不真正需要添加到 JSX 元素的 “attributes” 列表中。相反，你可以直接放置到元素的*内部*

由于这一技术的特殊性，当你在设计一个类似的 API 时，你或许会要直接地在你的 propTypes 里声明 children 的类型应为一个函数

```react
<Mouse children={mouse => (
  <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
)}/>

<Mouse>
  {mouse => (
    <p>鼠标的位置是 {mouse.x}，{mouse.y}</p>
  )}
</Mouse>
```

注意：

将 Render Props 与 React.PureComponent 一起使用时要小心

如果你在 render 方法里创建函数，那么使用 render prop 会抵消使用 [`React.PureComponent`](https://zh-hans.reactjs.org/docs/react-api.html#reactpurecomponent) 带来的优势。因为浅比较 props 的时候总会得到 false，并且在这种情况下每一个 `render` 对于 render prop 将会生成一个新的值。

render prop的优点：

- 支持ES6，和HOC一样
- 不用担心prop的命名问题，在render函数中只取需要的state
- 相较于HOC，不会产生无用的空组件加深层级
- 最重要的是，这里的构建模型是动态的，所有改变都在render中触发，能更好的利用react的生命周期。

## 组件懒加载

在React应用中，有些组件可能不经常用到，比如法律条款的弹窗，我们几乎不看，这些组件也就没有必要首次加载，可以在点击它们的时候再加载，这就需要动态引入组件，需要组件的时候，才引入组件，加载它们，进行渲染，也称为懒加载

### React.Lazy

React.lazy + Suspense

React 16.6添加了一个新的特性: React.lazy(), 它可以让代码分割(code splitting)更加容易

```react
const stockChartPromise = import("./StockChart");
// const OtherComponent = React.lazy(() => import('./OtherComponent'));
const StockChart = React.lazy(() => stockChartPromise);
```

原理：

import()函数返回的是promise, promise resolve后返回的是module对象(showMessage.js中暴露出来的对象)，通过module对象就可以调用showMessage中暴露的方法。当 Webpack 解析到该`import()`语法时，会自动进行代码分割。

对于最初 `React.lazy()` 所返回的 LazyComponent 对象，其 _status 默认是 -1，所以**首次渲染**时，会进入 readLazyComponentType 函数中的 default 的逻辑，这里才会真正异步执行 `import(url)`操作，由于并未等待，随后会检查模块是否 Resolved，如果已经Resolved了（已经加载完毕）则直接返回`moduleObject.default`（动态加载的模块的默认导出），否则将通过 throw 将 thenable 抛出到上层

https://juejin.cn/post/6844904191853494280

其他懒加载的库

### react-lazyload

安装

```shell
npm install --save react-lazyload
```

懒加载图片

```react
import React from 'react';
import ReactDOM from 'react-dom';
import LazyLoad from 'react-lazyload';
import MyComponent from './MyComponent';

const App = () => {
  return (
    <div className="list">
      <LazyLoad height={200}>
        <img src="tiger.jpg" /> /*
                                  Lazy loading images is supported out of box,
                                  no extra config needed, set `height` for better
                                  experience
                                 */
      </LazyLoad>
      <LazyLoad height={200} once >
                                /* Once this component is loaded, LazyLoad will
                                 not care about it anymore, set this to `true`
                                 if you're concerned about improving performance */
        <MyComponent />
      </LazyLoad>
      <LazyLoad height={200} offset={100}>
                              /* This component will be loaded when it's top
                                 edge is 100px from viewport. It's useful to
                                 make user ignorant about lazy load effect. */
        <MyComponent />
      </LazyLoad>
      <LazyLoad>
        <MyComponent />
      </LazyLoad>
    </div>
  );
};

ReactDOM.render(<App />, document.body);
```

默认懒加载组件

```react
import { lazyload } from 'react-lazyload';

@lazyload({
  height: 200,
  once: true,
  offset: 100
})
class MyComponent extends React.Component {
  render() {
    return <div>this component is lazyloaded by default!</div>;
  }
}
```



### @loadable/component

[Loadable Components](https://link.juejin.cn/?target=)是一个高阶组件，允许您将代码拆分为较小的块，并按需加载它们，就像React Lazy一样。使用Loadable Components，您可以指定在代码块加载时显示的加载组件

```react
import React from 'react';
import loadable from '@loadable/component';

const HomePage = loadable(() => import('./HomePage'), {
  fallback: <div>Loading...</div>,
});

const App = () => <HomePage />;
```



### react-imported-component

懒加载组件，相似组件有React.lazy react-loadable @loadable/component

使用预加载

```react
import importedComponent from 'react-imported-component';
const Component = importedComponent( () => import('./Component'));

const Component = importedComponent( () => import('./Component'), {
  LoadingComponent: Spinner, // what to display during the loading
  ErrorComponent: FatalError // what to display in case of error
});

Component.preload(); // force preload

// render it
<Component... />
```

懒加载与React.lazy基本相同

```react
import { lazy, LazyBoundary } from 'react-imported-component';
const Component = lazy(() => import('./Component'));

const ClientSideOnly = () => (
  <Suspense>
    <Component />
  </Suspense>
);

// or let's make it SSR friendly
const ServerSideFriendly = () => (
  <LazyBoundary>
    {' '}
    // LazyBoundary is Suspense* on the client, and "nothing" on the server
    <Component />
  </LazyBoundary>
);
```

hooks

```react
import {useImported} from 'react-imported-component'

const MyCalendarComponent = () => {
  const {
      imported: moment,
      loading
    } = useImported(() => import("moment"));

  return loading ? "..." : <span>today is {moment(Date.now).format()}</span>
}

// or we could make it a bit more interesting...

const MyCalendarComponent = () => {
  const {
      imported: format  = x => "---", // default value is used while importing library
    } = useImported(
      () => import("moment"),
      moment => x => moment(x).format // masking everything behind
    );

  return <span>today is {format(Date.now())</span>
}
```

### 动态导入

动态导入允许将代码分割成较小块并按需加载，而不是一次性加载所有内容。使用动态导入，您可以确保只加载特定功能所需的代码，从而实现较小的初始JavaScript包和更好的性能

```react
import React, { useState, useEffect } from 'react';

const HomePage = () => {
  const [module, setModule] = useState(null);

  useEffect(() => {
    import('./HomePageWidget').then((mod) => setModule(mod.default));
  }, []);

  return module ? <module /> : <div>Loading...</div>;
};
```

### 动态块

https://juejin.cn/post/7246686667749670971
