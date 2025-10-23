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

如果我们创建的state是一个一般数据类型，他就是一个不可变的值，如果需要改变我们需要重新创建一个state去覆盖它；但是如果我们的state是一个对象，我们在原对象上对其进行修改；有时候你会发现并不触发render，所以这里我们需要传入一个新的不可变对象。一般来讲直接用解决深拷贝的问题的方法就能解决；但是这种方式并不被官方推荐；因此我们需要借助immutable.js或者immer.js去生成一个不可变对象

```react
import react, {useState} from 'react'
export default function IndexPage() {
    const [list, setList] = useState([
        {
            id:1,
            value:'大饼'
        },
        {
            id:2,
            value:'豆浆'
        },
    ]);
  	// 改变对象后不会rerender
    const add = ()=>{
        list.push({
            id:3,
            value:'油条'
        })
        console.log(list.length)
        setList(list)
    }
    
    // 改变对象后会rerender
    const add = ()=>{
        setList([...list,{
            id:3,
            value:'油条'
        }])
    }
    return (
      <div>
          {list.map(item=><div key={item.id}>{item.value}</div>)}
          <button onClick={add}>add</button>
      </div>
  );
}
```

在这个案例中运行并点击你会发现，我们通过方法list的对象方法push给list插入一条值改变list的值，控制台会输出list的长度为3，然后我们将list传给setList并运行，但是我们的界面依旧没改变，依旧为运行前的大饼和豆浆；那是因为我们并没有创建一个新值覆盖原有的list只是在原对象上进行修改；而react并不能监听到这个变化；所以导致页面没有更新；所以需要去改变原来的list进行覆盖

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

https://rxjs-cn.github.io/learn-rxjs-operators/recipes/http-polling.md

https://github.com/btroncone/learn-rxjs

rxjs是一个库，它通过使用observable序列来编写异步和基于事件的程序。它提供了核心类型Observable，附属类型(observer、schedulers、subjects)和类似于数组的操作符(map、filter、reduce、every)等，这些操作符可以把异步事件作为集合来处理

- 异步常见问题
  - 竞态条件 (Race Condition)
    - 当多次发送请求时， 请求先后顺序就会影响到最终接收到的结果不同
  - 内存泄漏 (Memory Leak)
    - 例如页面初始化时有对 DOM 注册监听事件，而没有在适当的时机点把监听的事件移除，就有可能造成 Memory Leak。比如说在 A 页面监听 body 的 scroll 事件，但页面切换时，没有把 scroll 的监听事件移除
  - 复杂的状态 (Complex State)
    - 当有异步时，应用程式的状态就会变得非常复杂！比如说一个列表需要有权限才能看见具体的数据，首先需要获取这个列表数据，然后在根据用户的权限去展示数据，用户也可能翻页进行快速操作，这些都是异步执行，这时就会各种复杂的状态需要处理
  - 异常处理 (Exception Handling)
    - JavaScript 的 try/catch 可以捕捉同步的异常处理，但异步的就没这么容易，尤其当我们的异步行为很复杂时，这个问题就愈加明显。

无法统一的写法

- 我们除了要面对异步会遇到的各种问题外，还要烦恼很多不同的 api 写法，
  - DOM Events
  - XMLHttpRequest
  - fetch
  - WebSockets
  - Server Send Events
  - Service Worker
  - Node Stream
  - Timer
- 上面列的 api 都是异步的，但他们都有各自的 api 及写法。如果我们使用 rxjs，上面所有的 api 就可以透过 rxjs 来处理，能用同样的 api 来操作

可以把rxjs当作用来处理事件的lodash

rxjs中的基本概念：

Observable(是一个可观察对象)：表示一个概念，这个概念是一个可调用的未来值或事件的集合

Observer(观察者)：一个回调函数的集合，它指定如何监听由Observable提供的值

Subscription(订阅)：表示Observable的执行，它主要用于取消Obervable的执行

Operator(操作符)：

Subject(主体)：

Scheduler(调度器)：



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

### 

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

### 使用

转换为observables

```React
// 来自一个或多个值
Rx.Observable.of('foo', 'bar');

// 来自数组
Rx.Observable.from([1,2,3]);

// 来自事件
Rx.Observable.fromEvent(document.querySelector('button'), 'click');

// 来自 Promise
Rx.Observable.fromPromise(fetch('/users'));

// 来自回调函数(最后一个参数得是回调函数，比如下面的 cb)
// fs.exists = (path, cb(exists))
var exists = Rx.Observable.bindCallback(fs.exists);
exists('file.txt').subscribe(exists => console.log('Does file exist?', exists));

// 来自回调函数(最后一个参数得是回调函数，比如下面的 cb)
// fs.rename = (pathA, pathB, cb(err, result))
var rename = Rx.Observable.bindNodeCallback(fs.rename);
rename('file.txt', 'else.txt').subscribe(() => console.log('Renamed!'));

// RxJS v6+
import { timer, range, interval } from 'rxjs';

// 1秒后发出0，然后结束，因为没有提供第二个参数
const source = timer(1000);

const source = range(1, 10);
// 输出: 1,2,3,4,5,6,7,8,9,10
const example = source.subscribe(val => console.log(val));

const source = interval(1000);
// 数字: 0,1,2,3,4,5....
const subscribe = source.subscribe(val => console.log(val));
```

直接创建observables

```javascript
var myObservable = new Rx.Subject();
myObservable.subscribe(value => console.log(value));
myObservable.next('foo');

var myObservable = Rx.Observable.create(observer => {
  observer.next('foo');
  setTimeout(() => observer.next('bar'), 1000);
});
myObservable.subscribe(value => console.log(value));
```

控制流

```javascript
// 输入 "hello world"
var input = Rx.Observable.fromEvent(document.querySelector('input'), 'input');

// 过滤掉小于3个字符长度的目标值
input.filter(event => event.target.value.length > 2)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "hel"

// 延迟事件
input.delay(200)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "h" -200ms-> "e" -200ms-> "l" ...

// 每200ms只能通过一个事件
input.throttleTime(200)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "h" -200ms-> "w"

// 停止输入后200ms方能通过最新的那个事件
input.debounceTime(200)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "o" -200ms-> "d"

// 在3次事件后停止事件流
input.take(3)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "hel"

// 直到其他 observable 触发事件才停止事件流
var stopStream = Rx.Observable.fromEvent(document.querySelector('button'), 'click');
input.takeUntil(stopStream)
  .map(event => event.target.value)
  .subscribe(value => console.log(value)); // "hello" (点击才能看到)

```

事件常规写法

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

### 组合操作符

merge: 将多个 observables 转换成单个 observable 

```javascript
import { mapTo } from 'rxjs/operators';
import { interval, merge } from 'rxjs';

// 每2.5秒发出值
const first = interval(2500);
// 每2秒发出值
const second = interval(2000);
// 每1.5秒发出值
const third = interval(1500);
// 每1秒发出值
const fourth = interval(1000);

// 从一个 observable 中发出输出值
const example = merge(
  first.pipe(mapTo('FIRST!')),
  second.pipe(mapTo('SECOND!')),
  third.pipe(mapTo('THIRD')),
  fourth.pipe(mapTo('FOURTH'))
);
// 输出: "FOURTH", "THIRD", "SECOND!", "FOURTH", "FIRST!", "THIRD", "FOURTH"
const subscribe = example.subscribe(val => console.log(val));
```

mergeAll: 收集并订阅所有的 observables

```javascript
import { map, mergeAll } from 'rxjs/operators';
import { of } from 'rxjs';

const myPromise = val =>
  new Promise(resolve => setTimeout(() => resolve(`Result: ${val}`), 2000));
// 发出 1,2,3
const source = of(1, 2, 3);

const example = source.pipe(
  // 将每个值映射成 promise
  map(val => myPromise(val)),
  // 发出 source 的结果
  mergeAll()
);

/*
  输出:
  "Result: 1"
  "Result: 2"
  "Result: 3"
*/
const subscribe = example.subscribe(val => console.log(val));
```

zip **zip** 操作符会订阅所有内部 observables，然后等待每个发出一个值。一旦发生这种情况，将发出具有相应索引的所有值。这会持续进行，直到至少一个内部 observable 完成

```javascript
import { delay } from 'rxjs/operators';
import { of, zip } from 'rxjs';

const sourceOne = of('Hello');
const sourceTwo = of('World!');
const sourceThree = of('Goodbye');
const sourceFour = of('World!');
// 一直等到所有 observables 都发出一个值，才将所有值作为数组发出
const example = zip(
  sourceOne,
  sourceTwo.pipe(delay(1000)),
  sourceThree.pipe(delay(2000)),
  sourceFour.pipe(delay(3000))
);
// 输出: ["Hello", "World!", "Goodbye", "World!"]
const subscribe = example.subscribe(val => console.log(val));
```

startWith: 发出给定的第一个值

```javascript
import { startWith } from 'rxjs/operators';
import { of } from 'rxjs';

// 发出 (1,2,3)
const source = of(1, 2, 3);
// 从0开始
const example = source.pipe(startWith(0));
// 输出: 0,1,2,3
const subscribe = example.subscribe(val => console.log(val));
```

pairwise: 将前一个值和当前值作为数组发出

```javascript
import { pairwise, take } from 'rxjs/operators';
import { interval } from 'rxjs';

// 返回: [0,1], [1,2], [2,3], [3,4], [4,5]
interval(1000)
  .pipe(
    pairwise(),
    take(5)
  )
  .subscribe(console.log);
```

race: 使用首先发出值的 observable

```javascript
import { mapTo } from 'rxjs/operators';
import { interval } from 'rxjs/observable/interval';
import { race } from 'rxjs/observable/race';

// 接收第一个发出值的 observable
const example = race(
  // 每1.5秒发出值
  interval(1500),
  // 每1秒发出值
  interval(1000).pipe(mapTo('1s won!')),
  // 每2秒发出值
  interval(2000),
  // 每2.5秒发出值
  interval(2500)
);
// 输出: "1s won!"..."1s won!"...etc
const subscribe = example.subscribe(val => console.log(val));
```

concat: 按照顺序，前一个 observable 完成了再订阅下一个 observable 并发出值

```javascript
import { concat } from 'rxjs/operators';
import { of } from 'rxjs';

// 发出 1,2,3
const sourceOne = of(1, 2, 3);
// 发出 4,5,6
const sourceTwo = of(4, 5, 6);
// 先发出 sourceOne 的值，当完成时订阅 sourceTwo
const example = sourceOne.pipe(concat(sourceTwo));
// 输出: 1,2,3,4,5,6
const subscribe = example.subscribe(val =>
  console.log('Example: Basic concat:', val)
);
```

concatAll: 收集 observables，当前一个完成时订阅下一个

```javascript
import { map, concatAll } from 'rxjs/operators';
import { of, interval } from 'rxjs';

// 每2秒发出值
const source = interval(2000);
const example = source.pipe(
  // 为了演示，增加10并作为 observable 返回
  map(val => of(val + 10)),
  // 合并内部 observables 的值
  concatAll()
);
// 输出: 'Example with Basic Observable 10', 'Example with Basic Observable 11'...
const subscribe = example.subscribe(val =>
  console.log('Example with Basic Observable:', val)
);
```



```javascript
// RxJS v6+
import { timer, combineLatest } from 'rxjs';

// timerOne 在1秒时发出第一个值，然后每4秒发送一次
const timerOne = timer(1000, 4000);
// timerTwo 在2秒时发出第一个值，然后每4秒发送一次
const timerTwo = timer(2000, 4000);
// timerThree 在3秒时发出第一个值，然后每4秒发送一次
const timerThree = timer(3000, 4000);

// 当一个 timer 发出值时，将每个 timer 的最新值作为一个数组发出
const combined = combineLatest(timerOne, timerTwo, timerThree);

const subscribe = combined.subscribe(latestValues => {
  // 从 timerValOne、timerValTwo 和 timerValThree 中获取最新发出的值
    const [timerValOne, timerValTwo, timerValThree] = latestValues;
  /*
      示例:
    timerOne first tick: 'Timer One Latest: 1, Timer Two Latest:0, Timer Three Latest: 0
    timerTwo first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 0
    timerThree first tick: 'Timer One Latest: 1, Timer Two Latest:1, Timer Three Latest: 1
  */
    console.log(
      `Timer One Latest: ${timerValOne},
     Timer Two Latest: ${timerValTwo},
     Timer Three Latest: ${timerValThree}`
    );
  }
);
```



### 转换操作符

expand：递归调用提供的函数

```javascript
import { interval, of } from 'rxjs';
import { expand, take } from 'rxjs/operators';

// 发出 2
const source = of(2);
const example = source.pipe(
  // 递归调用提供的函数
  expand(val => {
    // 2,3,4,5,6
    console.log(`Passed value: ${val}`);
    // 3,4,5,6
    return of(1 + val);
  }),
  // 用5次
  take(5)
);
```

map对每个值投射处理函数

```javascript
import { from } from 'rxjs';
import { map } from 'rxjs/operators';

// 发出 (1,2,3,4,5)
const source = from([1, 2, 3, 4, 5]);
// 每个数字加10
const example = source.pipe(map(val => val + 10));
// 输出: 11,12,13,14,15
const subscribe = example.subscribe(val => console.log(val));
```

Mapto: 将每个发出值映射成常量

```javascript
import { fromEvent } from 'rxjs';
import { mapTo } from 'rxjs/operators';

// 发出每个页面点击
const source = fromEvent(document, 'click');
// 将所有发出值映射成同一个值
const example = source.pipe(mapTo('GOODBYE WORLD!'));
// 输出: (click)'GOODBYE WORLD!'...
const subscribe = example.subscribe(val => console.log(val));
```

concatMap: 将值映射成内部 observable，并按顺序订阅和发出

`concatMap` 和 [`mergeMap`](https://rxjs-cn.github.io/learn-rxjs-operators/operators/transformation/mergemap.html) 之间的区别。 因为 `concatMap` 之前前一个内部 observable 完成后才会订阅下一个， source 中延迟 2000ms 值会先发出。 对比的话， [`mergeMap`](https://rxjs-cn.github.io/learn-rxjs-operators/operators/transformation/mergemap.html) 会立即订阅所有内部 observables， 延迟少的 observable (1000ms) 会先发出值，然后才是 2000ms 的 observable 

```javascript
import { of } from 'rxjs';
import { concatMap, delay, mergeMap } from 'rxjs/operators';

// 发出延迟值
const source = of(2000, 1000);
// 将内部 observable 映射成 source，当前一个完成时发出结果并订阅下一个
const example = source.pipe(
  concatMap(val => of(`Delayed by: ${val}ms`).pipe(delay(val)))
);
// 输出: With concatMap: Delayed by: 2000ms, With concatMap: Delayed by: 1000ms
const subscribe = example.subscribe(val =>
  console.log(`With concatMap: ${val}`)
);

// 展示 concatMap 和 mergeMap 之间的区别
const mergeMapExample = source
  .pipe(
    // 只是为了确保 meregeMap 的日志晚于 concatMap 示例
    delay(5000),
    mergeMap(val => of(`Delayed by: ${val}ms`).pipe(delay(val)))
  )
  .subscribe(val => console.log(`With mergeMap: ${val}`));
```

mergeMap: 映射成 observable 并发出值

```javascript
import { of } from 'rxjs';
import { mergeMap } from 'rxjs/operators';

// 发出 'Hello'
const source = of('Hello');
// 映射成 observable 并将其打平
const example = source.pipe(mergeMap(val => of(`${val} World!`)));
// 输出: 'Hello World!'
const subscribe = example.subscribe(val => console.log(val));
```

concatMapTo 当前一个 observable 完成时订阅提供的 observable 并发出值

```javascript
import { of, interval } from 'rxjs';
import { concatMapTo, delay, take } from 'rxjs/operators';

// 每2秒发出值
const sampleInterval = interval(500).pipe(take(5));
const fakeRequest = of('Network request complete').pipe(delay(3000));
// 前一个完成才会订阅下一个
const example = sampleInterval.pipe(concatMapTo(fakeRequest));
// 结果
// 输出: Network request complete...3s...Network request complete'
const subscribe = example.subscribe(val => console.log(val));
```

pluck 选择observable中的属性发出

```javascript
import { from } from 'rxjs';
import { pluck } from 'rxjs/operators';

const source = from([{ name: 'Joe', age: 30 }, { name: 'Sarah', age: 35 }]);
// 提取 name 属性
const example = source.pipe(pluck('name'));
// 输出: "Joe", "Sarah"
const subscribe = example.subscribe(val => console.log(val));
```

Scan: 随着时间的推移进行归并

```javascript
import { interval } from 'rxjs';
import { scan, map, distinctUntilChanged } from 'rxjs/operators';

// 累加数组中的值，并随机发出此数组中的值
const scanObs = interval(1000)
  .pipe(
    scan((a, c) => [...a, c], []),
    map(r => r[Math.floor(Math.random() * r.length)]),
    distinctUntilChanged()
  )
  .subscribe(console.log);
```

switchmap: 映射成 observable，完成前一个内部 observable，发出值

```javascript
import { timer, interval } from 'rxjs';
import { switchMap } from 'rxjs/operators';

// 立即发出值， 然后每5秒发出值
const source = timer(0, 5000);
// 当 source 发出值时切换到新的内部 observable，发出新的内部 observable 所发出的值
const example = source.pipe(switchMap(() => interval(500)));
// 输出: 0,1,2,3,4,5,6,7,8,9...0,1,2,3,4,5,6,7,8
const subscribe = example.subscribe(val => console.log(val))
```

partition: 通过过滤条件将一个observable分成两个

```javascript
import { from, merge } from 'rxjs';
import { partition, map } from 'rxjs/operators';

const source = from([1, 2, 3, 4, 5, 6]);
// 第一个值(events)返回 true 的数字集合，第二个值(odds)是返回 false 的数字集合
const [evens, odds] = source.pipe(partition(val => val % 2 === 0));
/*
  输出:
  "Even: 2"
  "Even: 4"
  "Even: 6"
  "Odd: 1"
  "Odd: 3"
  "Odd: 5"
*/
const subscribe = merge(
  evens.pipe(map(val => `Even: ${val}`)),
  odds.pipe(map(val => `Odd: ${val}`))
).subscribe(val => console.log(val));
```

### 过滤操作符

first 发出第一个值或第一个通过给定表达式的值

```javascript
import { from } from 'rxjs';
import { first } from 'rxjs/operators';

const source = from([1, 2, 3, 4, 5]);
// 没有参数则发出第一个值
const example = source.pipe(first());
// 输出: "First value: 1"
const subscribe = example.subscribe(val => console.log(`First value: ${val}`));

const source = from([1, 2, 3, 4, 5]);
// 没有值通过的话则发出默认值
const example = source.pipe(first(val => val > 5, 'Nothing'));
// 输出: 'Nothing'
const subscribe = example.subscribe(val => console.log(val));
```

Last 根据提供的表达式，在源 observable 完成时发出它的最后一个值

```javascript
const source = from([1, 2, 3, 4, 5]);
// 发出最后一个偶数
const exampleTwo = source.pipe(last(num => num % 2 === 0));
// 输出: "Last to pass test: 4"
const subscribeTwo = exampleTwo.subscribe(val =>
  console.log(`Last to pass test: ${val}`)
);
```

takeUntil: 发出值，直到提供的 observable 发出值，它便完成

```javascript
import { interval, timer } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
// 5秒后发出值
const timer$ = timer(5000);
// 当5秒后 timer 发出值时， source 则完成
const example = source.pipe(takeUntil(timer$));
// 输出: 0,1,2,3
const subscribe = example.subscribe(val => console.log(val));
```

takewhile 发出值，直到提供的表达式结果为 false

```javascript
import { of } from 'rxjs';
import { takeWhile } from 'rxjs/operators';

// 发出 1,2,3,4,5
const source = of(1, 2, 3, 4, 5);
// 允许值发出直到 source 中的值大于4，然后便完成
const example = source.pipe(takeWhile(val => val <= 4));
// 输出: 1,2,3,4
const subscribe = example.subscribe(val => console.log(val));
```

distinct：过滤重复元素

```javascript
import { of } from 'rxjs';
import { distinct } from 'rxjs/operators';

of(1, 2, 3, 4, 5, 1, 2, 3, 4, 5)
  .pipe(distinct())
  // OUTPUT: 1,2,3,4,5
  .subscribe(console.log);
```



### 错误处理

catchError: 捕获拒绝的promise

```javascript
import { timer, from, of } from 'rxjs';
import { mergeMap, catchError } from 'rxjs/operators';

// 创建立即拒绝的 Promise
const myBadPromise = () =>
  new Promise((resolve, reject) => reject('Rejected!'));
// 1秒后发出单个值
const source = timer(1000);
// 捕获拒绝的 promise，并返回包含错误信息的 observable
const example = source.pipe(
  mergeMap(_ =>
    from(myBadPromise()).pipe(catchError(error => of(`Bad Promise: ${error}`)))
  )
);
// 输出: 'Bad Promise: Rejected'
const subscribe = example.subscribe(val => console.log(val));
```

retry: 如果发生错误，以指定次数重试 observable 序列

```javascript
import { interval, of, throwError } from 'rxjs';
import { mergeMap, retry } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
const example = source.pipe(
  mergeMap(val => {
    // 抛出错误以进行演示
    if (val > 5) {
      return throwError('Error!');
    }
    return of(val);
  }),
  // 出错的话可以重试2次
  retry(2)
);
```

Retrywhen: 当发生错误时，基于自定义的标准来重试 observable 序列

```javascript
const source = interval(1000);
const example = source.pipe(
  map(val => {
    if (val > 5) {
      // 错误将由 retryWhen 接收
      throw val;
    }
    return val;
  }),
  retryWhen(errors =>
    errors.pipe(
      // 输出错误信息
      tap(val => console.log(`Value ${val} was too high!`)),
      // 5秒后重启
      delayWhen(val => timer(val * 1000))
    )
  )
);
```

### 多播

多播是一个术语，它用来描述由单个 observable 发出的每个通知会被多个观察者所接收的情况。一个 observable 是否具备多播的能力取决于它是热的还是冷的

- 如果通知的生产者是观察者订阅 observable 时创建的，那么 observable 就是冷的。例如，[`timer`](http://cn.rx.js.org/class/es6/Observable.js~Observable.html#static-method-timer) observable 就是冷的，每次订阅时都会创建一个新的定时器。
- 如果通知的生产者不是每次观察者订阅 observable 时创建的，那么 observable 就是热的。例如，使用 [`fromEvent`](http://cn.rx.js.org/class/es6/Observable.js~Observable.html#static-method-fromEvent) 创建的 observable 就是热的，产生事件的元素存在于 DOM 之中，它不是观察者订阅时所创建的。

冷的 observables 是单播的，每个观察者所接收到的通知都是来自不同的生产者，生产者是观察者订阅时所创建的。

热的 observables 是多播的，每个观察者所接收到的通知都是来自同一个生产者。

有些时候，需要冷的 observable 具有多播的行为，RxJS 引入了 `Subject` 类使之成为可能

Subject 即是 observable，又是 observer (观察者)。通过使用观察者来订阅 subject，然后 subject 再订阅冷的 observable，可以让冷的 observable 变成热的。这是 RxJS 引入 subjects 的主要用途

```javascript
import { Observable } from "rxjs/Observable";
import { Subject } from "rxjs/Subject";
import "rxjs/add/observable/defer";
import "rxjs/add/observable/of";

const source = Observable.defer(() => Observable.of(
  Math.floor(Math.random() * 100)
));

function observer(name: string) {
  return {
    next: (value: number) => console.log(`observer ${name}: ${value}`),
    complete: () => console.log(`observer ${name}: complete`)
  };
}

const subject = new Subject<number>();
subject.subscribe(observer("a"));
subject.subscribe(observer("b"));
source.subscribe(subject);
```

要让 `source` 变成多播的，需要观察者订阅 subject，然后 subject 再订阅 `source` 。`source` 只会看到一个订阅 ( subscription )，它也只生成一个包含随机数的 `next` 通知和一个 `complete` 通知。Subject 会将这些通知发送给它的观察者

多播相关的方法

publish：共享源 observable 并通过调用 connect 方法使其变成热的

```javascript
import { interval } from 'rxjs';
import { publish, tap } from 'rxjs/operators';

// 每1秒发出值
const source = interval(1000);
const example = source.pipe(
  // 副作用只会执行1次
  tap(_ => console.log('Do Something!')),
  // 不会做任何事直到 connect() 被调用
  publish()
);

/*
  source 不会发出任何值直到 connect() 被调用
  输出: (5秒后)
  "Do Something!"
  "Subscriber One: 0"
  "Subscriber Two: 0"
  "Do Something!"
  "Subscriber One: 1"
  "Subscriber Two: 1"
*/
const subscribe = example.subscribe(val =>
  console.log(`Subscriber One: ${val}`)
);
const subscribeTwo = example.subscribe(val =>
  console.log(`Subscriber Two: ${val}`)
);

// 5秒后调用 connect，这会使得 source 开始发出值
setTimeout(() => {
  example.connect();
}, 5000);
```

multicast：使用提供 的 Subject 来共享源 observable

```javascript
import { Subject, interval } from 'rxjs';
import { take, tap, multicast, mapTo } from 'rxjs/operators';

// 每2秒发出值并只取前5个
const source = interval(2000).pipe(take(5));

const example = source.pipe(
  // 因为我们在下面进行了多播，所以副作用只会调用一次
  tap(() => console.log('Side Effect #1')),
  mapTo('Result!')
);


// 使用 subject 订阅 source 需要调用 connect() 方法
const multi = example.pipe(multicast(() => new Subject()));
/*
  多个订阅者会共享 source 
  输出:
  "Side Effect #1"
  "Result!"
  "Result!"
  ...
*/
const subscriberOne = multi.subscribe(val => console.log(val));
const subscriberTwo = multi.subscribe(val => console.log(val));
// 使用 subject 订阅 source
multi.connect();
```

Share:在多个订阅者间共享源 observable

```javascript
// RxJS v6+
import { timer } from 'rxjs';
import { tap, mapTo, share } from 'rxjs/operators';

// 1秒后发出值
const source = timer(1000);
// 输出副作用，然后发出结果
const example = source.pipe(
  tap(() => console.log('***SIDE EFFECT***')),
  mapTo('***RESULT***')
);

/*
  ***不共享的话，副作用会执行两次***
  输出: 
  "***SIDE EFFECT***"
  "***RESULT***"
  "***SIDE EFFECT***"
  "***RESULT***"
*/
const subscribe = example.subscribe(val => console.log(val));
const subscribeTwo = example.subscribe(val => console.log(val));

// 在多个订阅者间共享 observable
const sharedExample = example.pipe(share());
/*
   ***共享的话，副作用只执行一次***
  输出:
  "***SIDE EFFECT***"
  "***RESULT***"
  "***RESULT***"
*/
const subscribeThree = sharedExample.subscribe(val => console.log(val));
const subscribeFour = sharedExample.subscribe(val => console.log(val));
```

shareReplay: 共享源 observable 并重放指定次数的发出

当有副作用或繁重的计算时，你不希望在多个订阅者之间重复执行时，会使用 `shareReplay` 。 当你知道流的后来订阅者也需要访问之前发出的值，`shareReplay` 在这种场景下也是有价值的。 这种在订阅过程中重放值的能力是区分 [`share`](https://rxjs-cn.github.io/learn-rxjs-operators/operators/multicasting/share.html) 和 `shareReplay` 的关键

```javascript
// 使用 subject 模拟 url 的变化
const routeEnd = new Subject<{data: any, url: string}>();

// 提取 url 并与后来订阅者共享
const lastUrl = routeEnd.pipe(
  pluck('url'),
  share()
);

// 起始订阅者是必须的
const initialSubscriber = lastUrl.subscribe(console.log);

// 模拟路由变化
routeEnd.next({data: {}, url: 'my-path'});

// 没有任何输出
const lateSubscriber = lastUrl.subscribe(console.log);
```

### 工具函数

delay 根据给定时间延迟发出值

```javascript
import { of, merge } from 'rxjs';
import { mapTo, delay } from 'rxjs/operators';

// 发出一项
const example = of(null);
// 每延迟一次输出便增加1秒延迟时间
const message = merge(
  example.pipe(mapTo('Hello')),
  example.pipe(
    mapTo('World!'),
    delay(1000)
  ),
  example.pipe(
    mapTo('Goodbye'),
    delay(2000)
  ),
  example.pipe(
    mapTo('World!'),
    delay(3000)
  )
);
// 输出: 'Hello'...'World!'...'Goodbye'...'World!'
const subscribe = message.subscribe(val => console.log(val));
```

Delaywhen 延迟发出值，延迟时间由提供函数决定

```javascript
import { interval, timer } from 'rxjs';
import { delayWhen } from 'rxjs/operators';

// 每1秒发出值
const message = interval(1000);
// 5秒后发出值
const delayForFiveSeconds = () => timer(5000);
// 5秒后，开始发出 interval 延迟的值
const delayWhenExample = message.pipe(delayWhen(delayForFiveSeconds));
// 延迟5秒后输出值
// 例如， 输出: 5s....1...2...3
const subscribe = delayWhenExample.subscribe(val => console.log(val));
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

### observable hook

连接 RxJS Observable 与 React 组件。

安装

```shell
npm install observable-hooks rxjs react
```

在 observable-hooks 中我们可以用 [`useObservableState`](https://observable-hooks.js.org/zh-cn/api/#useobservablestate) 或 [`useObservableEagerState`](https://observable-hooks.js.org/zh-cn/api/#useobservableeagerstate)

使用

```react
import { pluckFirst, useObservableCallback } from 'observable-hooks'

function App(props) {
  const flag$ = useObservable(pluckFirst, [props.flag])

  const [onChange, textChange$] = useObservableCallback(
    event$ => event$.pipe(
      withLatestFrom(flag$),
      map(([event, flag]) => {
        return {
          text: event.currentTarget.value,
          flag
        }
      })
    )
  )
}
```



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



## million

https://github.com/aidenybai/million 

可以替换react的viturl dom框架



## 测试框架



