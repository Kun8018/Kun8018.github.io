---
title: React（七）
date: 2020-06-02 21:40:33
categories: IT
tags:
    - IT，Web,Node，React
toc: true
thumbnail: https://cdn.kunkunzhang.top/preact.jpeg

---

​      基于React的衍生库

<!--more-->

## immutablejs

### 数据可变会导致的问题

Immutable数据就是一旦创建，就不能更改的数据。每当对Immutable对象进行修改的时候，就会返回一个新的Immutable对象，以此来保证数据的不可变

有人说 Immutable 可以给 React 应用带来数十倍的提升，也有人说 Immutable 的引入是近期 JavaScript 中伟大的发明，因为同期 React 太火，它的光芒被掩盖了。这些至少说明 Immutable 是很有价值的。

Immutable的优点：

1.降低复杂度，避免副作用

2.节省内存。Immutable采用了结构共享机制，所以会尽量复用内存

3.方便回溯。Immutable每次修改都会创建新对象，且对象不变，那么变更记录就能够被保存下来。应用的状态变得可控、可追溯，方便撤销和重做功能的实现

4.函数式编程。Immutable本身就是函数式编程中的概念。纯函数式编程比面向对象更适用于前端开发，因为只要输入一致，输出必然是一致的，这样开发的组件更易于调试和组装

5.丰富的API

JavaScript 中的对象一般是可变的（Mutable），因为使用了引用赋值，新的对象简单的引用了原始对象，改变新的对象将影响到原始对象，比如

```javascript
var obj = {
 a: 1,
 b: 2
};
var obj1 = obj;obj1.a = 999;
obj.a //999
```

改变了obj1.a的值，同时也会更改到obj.a的值。

一般的解法就是使用「深拷贝」(deep copy)而非浅拷贝(shallow copy)，来避免被修改,但是这样造成了 CPU和内存的浪费.

immutable可以很好地解决这些问题

Immutable Data 就是一旦创建，就不能再被更改的数据。对 Immutable 对象的任何修改或添加删除操作都会返回一个新的 Immutable 对象。Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变。同时为了避免 deepCopy 把所有节点都复制一遍带来的性能损耗，Immutable 使用了 Structural Sharing（结构共享），即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享。

Immutable 的几种数据类型：

- List: 有序索引集，类似JavaScript中的Array。
- Map: 无序索引集，类似JavaScript中的Object。
- OrderedMap: 有序的Map，根据数据的set()进行排序。
- Set: 没有重复值的集合。
- OrderedSet: 有序的Set，根据数据的add进行排序。
- Stack: 有序集合，支持使用unshift（）和shift（）添加和删除。
- Range(): 返回一个Seq.Indexed类型的集合，这个方法有三个参数，start表示开始值，默认值为0，end表示结束值，默认为无穷大，step代表每次增大的数值，默认为1.如果start = end,则返回空集合。
- Repeat(): 返回一个vSeq.Indexe类型的集合，这个方法有两个参数，value代表需要重复的值，times代表要重复的次数，默认为无穷大。
- Record: 一个用于生成Record实例的类。类似于JavaScript的Object，但是只接收特定字符串为key，具有默认值。
- Seq: 序列，但是可能不能由具体的数据结构支持。
- Collection: 是构建所有数据结构的基类，不可以直接构建。

方法：

fromJS()：

`作用` : 将一个js数据转换为Immutable类型的数据 `用法` : `fromJS(value, converter)` `简介` : value是要转变的数据，converter是要做的操作。第二个参数可不填，默认情况会将数组准换为List类型，将对象转换为Map类型，其余不做操作。

is()

`作用` : 对两个对象进行比较 `用法` : `is(map1,map2)` `简介` : 和js中对象的比较不同，在js中比较两个对象比较的是地址，但是在Immutable中比较的是这个对象hashCode和valueOf，只要两个对象的hashCode相等，值就是相同的，避免了深度遍历，提高了性能

### 在react中使用

react中通常使用purecomponent进行props的浅比较，从而控制shouldComponentUpdate的返回值

但是当传入prop或者state不止一层，或者传入的是Array和Object类型时，浅比较就失效了，当然也可以在shouldComponentUpdate中使用deepCopy和deepCompare来避免不必要的render，但是深拷贝和深比较都是非常消耗性能的，此时可以用Immutable来进行优化

Immutable提供了简洁高效的判断数据是否变化的方法，只需===和is就能比较是否需要执行render，而这个操作几乎是零成本的，所以可以极大提高性能

```react
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import { is, Map } from 'immutable';

class Caculator extends Component {
  state = {
    counter: Map({ number: 0})
  }

	handleClick = () => {
    let amount = this.amount.value ? Number(this.amount.value): 0;
    let counter = this.state.counter.update('number', val => val + amount);
    this.setState({counter});
  }

	shouldComponentUpdate(nextProps={},nextState={}{
    if(Object.keys[this.state].length !== Object.keys(nextState).length){
    	return true;
  	}
  	for ( const key in nextState ) {
    	if( !is(this.state[key], nextState[key])) {
        return true;
      }
  	}    
		return false
  })
  render() {
    return (
    	<div>
      	<p>{ this.state.counter.get('number')}</p>
        <input ref={input => this.amout = input} />
        <button onClick="this.handleClick">+</button>
      </div>
    )
  }
}
ReactDOM.render(
	<Caculator/>,
  document.getElementById('root')
)
```

### 在redux中使用

可以使用redux-immutable中间件的方式实现redux与immutable搭配使用

建议把整个Redux的state树作为Immutable对象

### 注意

- 不要混合普通的JS对象和Immutable对象

- 把整个Redux的state树作为Immutable对象

- 除了展示组件，其他大部分组件都可以使用immutable对象提高效率

- 少用toJS方法，这个方法非常耗费性能，它会深度遍历数据转换成JS对象

- 你的Selector应该永远返回immutable对象

### 原理

Immutable.js 由 Facebook 花费 3 年时间打造，为前端开发提供了很多便利。我们知道 Immutable.js 采用了`持久化数据结构`，保证每一个对象都是不可变的，任何添加、修改、删除等操作都会生成一个新的对象，且通过`结构共享`等方式大幅提高性能

对于一个`持久化数据结构`，每次修改后我们都会得到一个新的版本，且旧版本可以完好保留。

Immutable.js 用树实现了`持久化数据结构`

假如我们要在`g`下面插入一个节点`h`，如何在插入后让原有的树保持不变？最简单的方法当然是重新生成一颗树

但这样做显然是很低效的，每次操作都需要生成一颗全新的树，既费时又费空间。

因此，我们新生成一个根节点，对于有修改的部分，把相应路径上的所有节点重新生成，对于本次操作没有修改的部分，我们可以直接把相应的旧的节点拷贝过去，这其实就是`结构共享`。这样每次操作同样会获得一个全新的版本（根节点变了，新的`a`!==旧的`a`），历史版本可以完好保留，同时也节约了空间和时间。

至此我们发现，用树实现`持久化数据结构`还是比较简单的，Immutable.js提供了多种数据结构

对于一个map，我们完全可以把它视为一颗扁平的树，与上文实现`持久化数据结构`的方式一样，每次操作后生成一个新的对象，把旧的值全都依次拷贝过去，对需要修改或添加的属性，则重新生成。这其实就是`Object.assign`

在实现`持久化数据结构`时，Immutable.js 参考了`Vector Trie`这种数据结构（其实更准确的叫法是`persistent bit-partitioned vector trie`或`bitmapped vector trie`，这是Clojure里使用的一种数据结构，Immutable.js 里的相关实现与其很相似）

假如我们有一个 map ，key 全都是数字（当然你也可以把它理解为数组）`{0: ‘banana’, 1: ‘grape’, 2: ‘lemon’, 3: ‘orange’, 4: ‘apple’}`，为了构造一棵二叉`Vector Trie`，我们可以先把所有的key转换为二进制的形式：`{‘000’: ‘banana’, ‘001’: ‘grape’, ‘010’: ‘lemon’, ‘011’: ‘orange’, ‘100’: ‘apple’}`

对于一个 key 全是数字的map，我们完全可以通过一颗`Vector Trie`来实现它，同时实现`持久化数据结构`。如果key不是数字怎么办呢？用一套映射机制把它转成数字就行了。 Immutable.js 实现了一个[hash](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Ffacebook%2Fimmutable-js%2Fblob%2Fe65e5af806ea23a32ccf8f56c6fabf39605bac80%2Fsrc%2FHash.js%23L10%3A17)函数，可以把一个值转换成相应数字。

这里为了简化，每个节点数组长度仅为2，这样在数据量大的时候，树会变得很深，查询会很耗时，所以可以扩大数组的长度，Immutable.js 选择了32

因为采用了`结构共享`，在添加、修改、删除操作后，我们避免了将 map 中所有值拷贝一遍，所以特别是在数据量较大时，这些操作相比`Object.assign`有明显提升

然而，查询速度似乎减慢了？我们知道 map 里根据 key 查找的速度是`O(1)`，这里由于变成了一棵树，查询的时间复杂度变成了`O(log N)`，因为是 32 叉树，所以准确说是O(log32 N)

`HAMT`全称`hash array mapped trie`，其基本原理与上篇所说的`Vector Trie`非常相似，不过它会对树进行压缩，以节约一些空间

https://juejin.cn/post/6844903682891333640

## Immer.js

[Immer](https://link.segmentfault.com/?enc=CsdrOwxqIW8RSSW4sJk%2FQg%3D%3D.FqvneTCQUrfuLPKN4RLTkOhn0oZZMia3QXrQd3%2BIa9SJvlwmvOimc9BvUKRgzh1i) 是 mobx 的作者写的一个 immutable 库，核心实现是利用 ES6 的 proxy，几乎以最小的成本实现了 js 的不可变数据结构，简单易用、体量小巧、设计巧妙，满足了我们对JS不可变数据结构的需求。

核心概念：

- currentState： 被操作对象的最初状态
- draftState： 根据 currentState 生成的草稿状态，它是 currentState 的代理，对 draftState 所做的任何修改都将被记录并用于生成 nextState 。在此过程中，currentState 将不受影响
- nextState：根据 draftState 生成的最终状态
- produce： 生产，用来生成 nextState 或 producer 的函数
- producer ：生产者，通过 produce 生成，用来生产 nextState ，每次执行相同的操作
- recipe： 生产机器，用来操作 draftState 的函数

使用

```javascript
import { produce } from 'immer'

let nextState = produce(currentState, (draft) => {

})

let producer = produce((draft) => {
  draft.x = 2
});
let nextState = producer(currentState);

currentState === nextState; // true
```

patch布丁功能

通过此功能，可以方便进行详细的代码调试和跟踪，可以知道 recipe 内的做的每次修改，还可以实现时间旅行

```javascript
import produce, { applyPatches } from "immer"

let state = {
  x: 1
}

let replaces = [];
let inverseReplaces = [];

state = produce(
  state,
  draft => {
    draft.x = 2;
    draft.y = 2;
  },
  (patches, inversePatches) => {
    replaces = patches.filter(patch => patch.op === 'replace');
    inverseReplaces = inversePatches.filter(patch => patch.op === 'replace');
  }
)

state = produce(state, draft => {
  draft.x = 3;
})
console.log('state1', state); // { x: 3, y: 2 }

state = applyPatches(state, replaces);
console.log('state2', state); // { x: 2, y: 2 }

state = produce(state, draft => {
  draft.x = 4;
})
console.log('state3', state); // { x: 4, y: 2 }

state = applyPatches(state, inverseReplaces);
console.log('state4', state); // { x: 1, y: 2 }
```

### use-immer

immer.js的hook写法

```javascript
import React, { useCallback } from "react";
import { useImmer } from "use-immer";

const TodoList = () => {
  const [todos, setTodos] = useImmer([
    {
      id: "React",
      title: "Learn React",
      done: true
    },
    {
      id: "Immer",
      title: "Try Immer",
      done: false
    }
  ]);

  const handleToggle = useCallback((id) => {
    setTodos((draft) => {
      const todo = draft.find((todo) => todo.id === id);
      todo.done = !todo.done;
    });
  }, []);

  const handleAdd = useCallback(() => {
    setTodos((draft) => {
      draft.push({
        id: "todo_" + Math.random(),
        title: "A new todo",
        done: false
      });
    });
  }, []);
```

### useImmerReducer

```javascript
import React, { useCallback } from "react";
import { useImmerReducer } from "use-immer";

const TodoList = () => {
  const [todos, dispatch] = useImmerReducer(
    (draft, action) => {
      switch (action.type) {
        case "toggle":
          const todo = draft.find((todo) => todo.id === action.id);
          todo.done = !todo.done;
          break;
        case "add":
          draft.push({
            id: action.id,
            title: "A new todo",
            done: false
          });
          break;
        default:
          break;
      }
    },
    [ /* initial todos */ ]
  );
}
```



https://immerjs.github.io/immer/update-patterns

## rxjs

rxjs是一个库，它通过使用observable序列来编写异步和基于事件的程序。它提供了核心类型Observable，附属类型(observer、schedulers、subjects)和类似于数组的操作符(map、filter、reduce、every)等，这些操作符可以把异步事件作为集合来处理

可以把rxjs当作用来处理事件的lodash

rxjs中的基本概念：

Observable(是一个可观察对象)：表示一个概念，这个概念是一个可调用的未来值或事件的集合

Observer(观察者)：一个回调函数的集合，它指定如何监听由Observable提供的值

Subscription(订阅)：表示Observable的执行，它主要用于取消Obervable的执行

Operator(操作符)：

Subject(主体)：

Scheduler(调度器)：

### 安装

通过npm安装

```shell
npm install rxjs
```

通过es6或者commonjs导入

```javascript
var Rx = require('rxjs/Rx')
import Rx from 'rxjs/Rx'

Rx.observable.of(1,2,3)//等等
```

按需导入函数(可以减少打包体积)

```javascript
import { Observable } from 'rxjs/observable'
import 'rxjs/add/observable/of';
import 'rxjs/add/operator/map'

var Observable = require('rxjs/Observable').Observable
require('rxjs/add/observable/of')
require('rxjs/add/operator/map')

Observable.of(1,2,3).map(x => x+ '!!!');//等等
```

### 注册事件

常规写法

```javascript
var button = document.querySelector('button')
button.addEventListener('click',()=> console.log('click'))
```

rxjs写法

```javascript
var button = document.querySelector('button')
Rx.observable.fromEvent(button,'click')
  .subscribe(()=> console.log('click'))
```

### 操作变量

常规写法是非纯函数，状态管理较乱

```javascript
var count = 0;
var button = document.querySelector('button')
button.addEventListener('click',()=> console.log(`click ${{++count}}`))
```

Rxjs将应用状态隔离起来

```javascript
var button = document.querySelector('button')
Rx.observable.fromEvent(button,'click')
  .scan(count => count +1,0)
  .subscribe(count => console.log(`click ${count}`))
```

其他对变量的操作函数

```javascript
//获取输入框
var input = Rx.Observable.fromEvent(document.querySelector('input'),'input')

//传递一个新值

//传递两个新值
input.plunk('target','value').pairwise()
    .subsribe(value => console.log(value))

//只通过唯一的值
input.plunk('data').distinct()
    .subsribe(value => console.log(value))

//不传递重复值
input.plunk('data').
```

### 观察者模式与迭代器模式

Rxjs中包含两个基本概念：Observable和Observer

Observable作为被观察者，是一个可调用的未来值或事件的集合，支持异步或者同步数据流

Observer作为观察者，是一个回调函数的集合，他知道如何去监听由Observable提供的值

Observer与Observable之间是观察者模式，Observer通过Observable提供的subscribe方法订阅，Observable通过Observer提供的next方法向Observer发布事件

在Rxjs中，Observer除了有next方法来接收Observable的事件外，还提供了另外的两个方法：error方法和complete方法，来完成异常和完成状态，这个就是迭代器模式，类似于ES6中的Iterator遍历器

```react
import { Observable } from 'rxjs'

const observer = {
  next: (value) => console.log(value);
  error: err => console.error('Observer got an error' + err);
	complete: () => console.log('Observer got a complete notification')
}

const observable = new Observable (function(observer) {
  observer.next('a');
  observer.next('b');
  observer.complete();
  
  observer.next('c')
})

const subscription = observable.subscribe(observer)
```



### react使用

在react中，在componentDidMount生命周期中订阅observable，在componentWillUnmount中取消订阅

```react
import messages from './someObservable'

class Mycomponent extends ObservableComponent{
  constructor(props){
    super(props);
    this.state = {message:[]};
  }
  componentDidMount(){
    this.messages = messages
       .scan(messages,messages) => [messages].concat(messages,[])
       .subscribe(messages => this.setState({messages:messages}))
  }
  componentWillUnmount(){
    this.messages.unsubscribe();
  }
  render() {
    return (
      <div>
        <ul>
           {this.state.messages.map(message => <li>{message.text}</li>)}
        </ul>
      </div>
    );
  }
}
 
export default MyComponent;
```



### Observables与promise

单值与多值

```javascript
const numberPromise = new Promise((resolve) => {
    resolve(5);
    resolve(10)
});

numberPromise.then(value => console.log(value));  //. 5

const Observable = require('rxjs/Observable').Observable;
// observables的写法，使用 next 替代 promise 的 resolve， 用subscribe 取代then来订阅结果。
const numberObservable = new Observable((observer) => {
    observer.next(5);
    observer.next(10);
});

numberObservable.subscribe(value => console.log(value));

// 输出 5 10
```

执行机制：promise是立即执行，observable有subscribe才执行

```javascript
const promise = new Promise((resolve) => {
    console.log('promise call')
    resolve(1);
    console.log('promise end')
})

// 执行这段代码 promise call 和 promise end 会立即执行
const observable = new Observable(() => {
    console.log('I was called!');
});

// 此时并没有console

// 只有 observable.subscribe(); 这个时候 I was called！才会被打印出来。
```

promise不可取消，observables可取消

```javascript
const Observable = require('rxjs/Observable').Observable;

const observable = new Observable((observer) => {
    let i = 0;
    const token = setInterval(() => {
        observer.next(i++);
    }, 1000);
  
    return () => clearInterval(token);
});

const subscription = observable.subscribe(value => console.log(value + '!'));

setTimeout(() => {
    subscription.unsubscribe();
}, 5000)

// 结果
// 0!
// 1!
// 2!
// 3!
```

observables可以被多次执行，promise 是比较激进的，在一个promise被创建的时候，他就已经执行了，并且不能重复的被执行了。

```javascript
let time;
const waitOneSecondPromise = new Promise((resolve) => {
    console.log('promise call')
    time = new Date().getTime();
    setTimeout(() => resolve('hello world'), 1000);
});

waitOneSecondPromise.then((value) => {console.log( '第一次', value, new Date().getTime() - time)});

setTimeout(() => {
    waitOneSecondPromise.then((value) => {console.log('第二次', value, new Date().getTime() - time)});   
}, 5000)

// 输出结果是 promise call
第一次 hello world 1007
第二次 hello world 5006

```

observable

```javascript
const Observable = require('rxjs/Observable').Observable;

let time;
const waitOneSecondObservable = new Observable((observer) => {
    console.log('I was called');
    time = new Date().getTime();
    setTimeout(() => observer.next('hey girl'), 1000);
});

waitOneSecondObservable.subscribe((value) => {console.log( '第一次', value, new Date().getTime() - time)});

setTimeout(() => {
    waitOneSecondObservable.subscribe((value) => {console.log( '第二次', value, new Date().getTime() - time)});
}, 5000)

// 输出
I was called
第一次 hey girl 1003
I was called
第二次 hey girl 1003
```

用observable已经可以实现多次订阅，但是这有时候可能不能符合我们的业务场景，在http请求中，我们可能希望只发一次请求，但是结果被多个订阅者共用。 Observables 本身没有提供这个功能，我们可以用 RxJS 这个库来实现，它有一个 share 的 operator

```javascript
const waitOneSecondObservable = new Observable((observer) => {
    // 发送http请求
});

const sharedWaitOneSecondObservable = 
    waitOneSecondObservable.share();

sharedWaitOneSecondObservable.subscribe(doSomething);

sharedWaitOneSecondObservable.subscribe(doSomethingElse);

// 使用了share，虽然subscribe了多次，但是仅发送一次请求，share了结果。
```

promise是异步函数，而observable可以根据需求是否使用异步

```javascript
const promise = new Promise((resolve) => {
    resolve(5);
});

promise.then(value => console.log(value + '!'));

console.log('And now we are here.');

// 
And now we are here.
5!
const Observable = require('rxjs/Observable').Observable;

const observable = new Observable((observer) => {
    // observer.next(5);
    setTimeout(() => {
        observer.next(5);
    })
});

observable.subscribe(value => console.log(value + '!'));
console.log('And now we are here.');

// 
这个如果是直接next 5,则输出是  5！ -> And now we are here.
采用setTimeout next 5， 则相反  And now we are here.-> 5！
```

rxjs中有一些操作符可以让监听强制为异步的方式，例如 observeOn。



https://www.jianshu.com/p/273e7ab02fa1

## cyclejs



## Ramda

ramda的主要特性：

Ramda强调更加纯粹的函数式编程风格，数据不变性和无副作用是其核心设计理念，可以帮助你使用简洁优雅的代码完成工作

Ramda函数本身都是自动柯里化的，这可以让你在只提供部分参数的情况下，轻松在已有函数的基础上创建新的函数

Ramda函数参数的排列顺序更便于柯里化，要操作的数据通常在最后面。

Ramda 的数据一律放在最后一个参数，理念是"**function first，data last**"

### 安装

安装

```shell
npm install ramda 
```

全部引入

```javascript
const R = require('ramda')

import * as R from 'ramda'

const {identity} = RR.map(identity,[1,2,3]) 
```

部分引入

```javascript
import identity from 'ramda/src/identity'
```

用起来功能和lodash差不多，

https://www.ruanyifeng.com/blog/2017/03/ramda.html

## Preact

### 基本概念

启动-安装preact-cli

```shell
npm i -g preact-cli
```

创建应用

```shell
preact create my-first-preact-app
cd my-first-preact-app
```

启动

```shell
npm start
```

在本地端口8080就可以访问

打包构建

```shell
npm run build
```

preact打包构建很快，且和pwa配合很好，一些移动端的页面以及活动页建议可以尝试一下，性能确实会比React好一些，开发与构建流程也很简单高效。

传统有状态组件与无状态组件

有状态组件

```javascript
class Link extends Component {
    render(props, state) {
        return <a href={props.href}>{ props.children }</a>;
    }
}
```

上面的代码就用到了**PReact可以直接在render中传入props和state**的特性，从一定程度上简化了写法，提升了可读性。

无状态组件



**关联状态**

在优化 state 改变的方面，Preact 比 React 走得更超前一点。在 ES2015 React 代码中，通常的模式是在 render() 方法中使用箭头函数，以便响应事件，更新状态。**每次渲染都再局部创建一个函数闭包，效率十分低下，而且会迫使垃圾回收器作许多不必要的工作。**

在 Preact 的 Form 中，提供了 linkState() 作为解决方案。linkState() 是 Component 类的一个内置方法。

当发生一个事件时，调用 .linkState('text') 将返回一个处理器函数，这个函数把它相关的值更新到组件状态内指定的值。 **多次调用 linkState(name)时，如果 name 参数相同，那么结果会被缓存起来**。所以就必然不存在**性能**问题，如:

```javascript
class Foo extends Component {
    render({ }, { text }) {
        return <input value={text} onInput={this.linkState('text')} />;
    }
}
```

**外部DOM修改**

有时，需要用到一些第三方库，这些**第三方库需要能够自由的修改 DOM，并且在 DOM 内部持久化状态，或这些第三方库根本就没有组件化**。有许多优秀的 UI 工具或可复用的元素都是处于这种无组件化的状态。在 Preact 中 (React 中也类似), 使用这样的库需要告诉 Virtual DOM 的 rendering/diffing 算法：在给定的组件(或者该组件所呈现的 DOM) 中不要去撤销任何外部 DOM 的改变。

可以在组件中定义一个 shouldComponentUpdate() 方法并让其返回值为 fasle：

```javascript
class Block extends Component {
  shouldComponentUpdate = () => false;
}
```

有了这个生命周期的钩子（shouldComponentUpdate），并**告诉 Preact 当 VDOM tree 发生状态改变的时候, 不要去再次渲染该组件**。这样**组件就有了一个自身的根 DOM** 元素的引用。你**可以把它当做一个静态组件**，直到被移除。因此，任何的组件引用都可以简单通过 this.base 被调用，并且对应从 render() 函数返回的根 JSX 元素。

### Signal

Preact 引入了 Signals，提供了快速的响应式状态原语（或者叫原子吧）， Signals 有以下几点：

- 感觉上像是使用原始数据结构
- 能根据值的变化自动更新
- 直接更新 DOM （换句话来说无 VDOM）
- 没有依赖数组

使用

```react
import { signal } from "@preact/signals";

const count = signal(0);

function Counter() {
  const value = count.value;

  const increment = () => {
    count.value++;
  }

  return (
    <div>
      <p>Count: {value}</p>
      <button onClick={increment}>click me</button>
    </div>
  );
}
```

Preact的signal跟 **SolidJS** 的 `createSignal`非常相似，而且两者有很多共同点（下面再说），另外通过`.value`访问属性非常类似于 Vue 中的 Ref。Signals 可以在一个应用从小到大，在越来越复杂的逻辑迭代后，依然能保证性能。Singals 提供了细粒度状态管理的好处，而无需通过 memorize 或者其他 tricks 方式去优化，Signals 跳过了数据在组件树中的传递，而是直接更新所引用的组件。这样开发者就能降低使用心智，保证性能最佳

能达到如此表现，Signals 有以下几点：

- 默认惰性求值（lazy evaluate）- 只有被使用到的才会被监听和更新
- 最佳更新策略
- 最佳依赖追踪策略 - 不像 hooks 需要指定依赖
- 直接访问状态值，不需要 selector 或其他 hooks



### 性能监控

Preact 很适用于 PWA，它也可以与许多其他工具和技术一起使用以进一步提升和监控性能，

1. [**webpack的代码拆分按需加载**](https://webpack.github.io/docs/code-splitting.html) 来分解代码，以便**只发送用户页面需要的代码**。根据需要延迟加载其余部分可提高页面加载时间。
2. [**Service Worker 缓存**](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)允许**离线缓存应用程序中的静态和动态资源**，实现即时加载和重复访问时更快的交互性。使用[sw-precache](https://github.com/GoogleChrome/sw-precache#wrappers-and-starter-kits)或[offline-plugin](https://github.com/NekR/offline-plugin)完成此操作。
3. [**PRPL**](https://developers.google.com/web/fundamentals/performance/prpl-pattern/)鼓励**向浏览器预先推送或预加载资源**，从而加快后续页面的加载速度。它基于代码拆分和 SW 缓存。
4. [**Lighthouse**](https://github.com/GoogleChrome/lighthouse/)允许你**审计（监控）渐进式 Web 应用程序的性能和最佳实践**，因此你能知道你的应用程序的表现情况。



### 把React替换为Preact

两种方式：
（1）安装 preact-compat
（2）把 React 的入口替换为 preact，并解决代码冲突

### 优缺点

优点：

1.接近于实质：Preact 实现了一个可能是最薄的一层虚拟 DOM。它将虚拟 DOM 与 DOM 本身区别开，注册真实的事件处理函数，很好地与其它库一起工作。

2.小体积、轻量：大多数 UI 框架相当大，在应用程序js代码中占比较高。Preact却足够小，你的业务代码，是应用程序中最大的部分。**preact本身的bundle在gzip压缩后大概只有3kb，比React小很多**。更少js代码的加载，解析和执行，可以有效的提升应用的性能与体验。

3.快速、高性能：Preact 是快速的，不仅因为它的体积，**一个更简单和可预测的 diff 实现**，使它成为最快的虚拟 DOM 框架之一。它也包含**额外的性能优化特性**，如：批量自定义更新，可选的异步渲染，DOM 回收和通过关连状态优化的事件处理等。

4.易于开发和生产：在不需要牺牲生产力的前提，preact包含了有一些额外而便捷的功能以使得开发更简单高效，如：

props, state 和 context 可以被传递给 render()；
**可使用标准的 HTML 属性**，如 class 和 for；
可使用 React 开发工具等。

5.与react生态兼容：可以无缝使用 React 生态系统中可用的数千个组件。增加一个简单的兼容层 preact-compat 到绑定库中，甚至可以在系统中使用非常复杂的 React 组件。

6.可以很容易的和[PWA（渐进式 Web 应用程序）](https://developers.google.com/web/progressive-web-apps/)配合工作，提供更好的用户体验：PReact官方的脚手架[preact-cli](https://github.com/developit/preact-cli)可以直接快速的构建一个PReact的渐进式 Web 应用程序。使得页面在加载的 [5 秒内就进行交互](https://infrequently.org/2016/09/what-exactly-makes-something-a-progressive-web-app/)。

### 与react对比



## 测试框架



