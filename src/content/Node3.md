---
title: Javascript开发（二）
date: 2021-01-15 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/jsEvent.jpg
---

​     第二篇主要讲原生js语言的原生方法，如继承、闭包和原型链等

<!--more-->

### 事件

#### 事件级别

事件级别分为Dom0级、Dom2级和Dom3级

Dom0级指在JavaScript中指定对象，最后通过事件处理属性赋值null来解绑事件

```html
<body>
  <button value="按钮"></button>
  <script>
    var button = document.querySelector("button");
    btn.click = function(){ alert("0")}
  </script>
</body>
```

缺点：

1.不能给同一元素添加多个事件，如果添加多个事件会互相覆盖

2.不能控制事件流

Dom2级事件是对指定对象添加事件处理函数，可以是多个，如

```html
<body>
  <div id="demo"></div>
  <script>
     demo.addEventListener("onclick",clickfn,false);
     demo.addEventListener("mouseover",showfn,false);
    function clickfn(){
      alert('1')
    }
    function showfn(){
      alert('2')
    }
  </script>
</body>
```

特别地，IE8版本以下的IE浏览器下不支持addEventListener和removeEventListener，使用attachEvent和detachEvent

Dom2与Dom0之间不会互相覆盖

Dom3级是在Dom2级的基础上添加更多事件类型，

UI事件，主要包括load,unload,abort,error,select,resize,scroll事件。

焦点事件，当用户获得或失去焦点时触发，如focus、blur

鼠标事件，当用户在鼠标上操作时触发事件，如dbclick、mouseup

键盘事件、当用户在键盘上操作时触发事件，如keydown，keypress

文本事件：当在文档中输入文本时触发，如textInput

滚轮事件，当用户使用鼠标滚轮或者类似设备时触发事件，如mousewheel

合成事件：当为IME(输入法编辑器)输出字符时触发，如compositionstart

变动事件：当底层Dom结构发生变化时触发，如DomsubtreeModified

html5事件、设备事件

Dom3也允许自定义事件

事件模型是指分为三个阶段：

- 捕获阶段：在事件冒泡的模型中，捕获阶段不会响应任何事件；
- 目标阶段：目标阶段就是指事件响应到触发事件的最底层元素上；
- 冒泡阶段：冒泡阶段就是事件的触发响应会从最底层目标一层层地向外到最外层（根节点），事件代理即是利用事件冒泡的机制把里层所需要响应的事件绑定到外层；

#### EventTarget对象

DOM 的事件操作（监听和触发），都定义在`EventTarget`接口。所有节点对象都部署了这个接口，其他一些需要事件通信的浏览器内置对象（比如，`XMLHttpRequest`、`AudioNode`、`AudioContext`）也部署了这个接口。

`EventTarget.addEventListener()`用于在当前节点或对象上，定义一个特定事件的监听函数。一旦这个事件发生，就会执行监听函数。该方法没有返回值。

addEventListener()有三个参数：

type：表示监听事件类型的字符串，比如click等

listener：监听函数

options：指定有关listener属性等可选参数对象。在旧版的DOM规定中addEventListener的第三个参数是布尔值表示是否在捕获阶段调用事件处理程序。随着时间的推移，很明显需要更多的选项。与其在后面添加更多的参数，不如将第三个参数改为一个包含各种属性的对象

​				capture：布尔值，表示listener会在该类型的事件捕获阶段传播到该EventTarget时触发

​				once：布尔值，表示listener在添加之后最多只被调用一次，如果是true，listener会在其被调用之后自动移除

​				passive：设置为true时，表示listener永远不会调用preventDefault。如果listener仍然调用了这个函数，客户端将会忽略它并抛出一个警告

​				

`EventTarget.removeEventListener`方法用来移除`addEventListener`方法添加的事件监听函数。

`EventTarget.dispatchEvent`方法在当前节点上触发指定事件，从而触发监听函数的执行。该方法返回一个布尔值，只要有一个监听函数调用了`Event.preventDefault()`，则返回值为`false`，否则为`true`。

#### Event对象

事件发生以后，会产生一个事件对象，作为参数传给监听函数。浏览器原生提供一个`Event`对象，所有的事件都是这个对象的实例，或者说继承了`Event.prototype`对象。

实例属性

`Event.bubbles`属性返回一个布尔值，表示当前事件是否会冒泡。该属性为只读属性，一般用来了解 Event 实例是否可以冒泡。前面说过，除非显式声明，`Event`构造函数生成的事件，默认是不冒泡的。

`Event.eventPhase`属性返回一个整数常量，表示事件目前所处的阶段。该属性只读。

- 0，事件目前没有发生。
- 1，事件目前处于捕获阶段，即处于从祖先节点向目标节点的传播过程中。
- 2，事件到达目标节点，即`Event.target`属性指向的那个节点。
- 3，事件处于冒泡阶段，即处于从目标节点向祖先节点的反向传播过程中。

`Event.cancelable`属性返回一个布尔值，表示事件是否可以取消。该属性为只读属性，一般用来了解 Event 实例的特性。

事件发生以后，会经过捕获和冒泡两个阶段，依次通过多个 DOM 节点。因此，任意事件都有两个与事件相关的节点，一个是事件的原始触发节点（`Event.target`），另一个是事件当前正在通过的节点（`Event.currentTarget`）。前者通常是后者的后代节点。

`Event.currentTarget`属性返回事件当前所在的节点，即事件当前正在通过的节点，也就是当前正在执行的监听函数所在的那个节点。随着事件的传播，这个属性的值会变。

`Event.target`属性返回原始触发事件的那个节点，即事件最初发生的节点。这个属性不会随着事件的传播而改变。

`Event.type`属性返回一个字符串，表示事件类型。事件的类型是在生成事件的时候指定的。该属性只读。

`Event.timeStamp`属性返回一个毫秒时间戳，表示事件发生的时间。它是相对于网页加载成功开始计算的。

`Event.isTrusted`属性返回一个布尔值，表示该事件是否由真实的用户行为产生。比如，用户点击链接会产生一个`click`事件，该事件是用户产生的；`Event`构造函数生成的事件，则是脚本产生的。

`Event.detail`属性只有浏览器的 UI （用户界面）事件才具有。该属性返回一个数值，表示事件的某种信息。具体含义与事件类型相关。比如，对于`click`和`dblclick`事件，`Event.detail`是鼠标按下的次数（`1`表示单击，`2`表示双击，`3`表示三击）；对于鼠标滚轮事件，`Event.detail`是滚轮正向滚动的距离，负值就是负向滚动的距离，返回值总是3的倍数。

实例方法

`Event.preventDefault`方法取消浏览器对当前事件的默认行为。比如点击链接后，浏览器默认会跳转到另一个页面，使用这个方法以后，就不会跳转了；再比如，按一下空格键，页面向下滚动一段距离，使用这个方法以后也不会滚动了。该方法生效的前提是，事件对象的`cancelable`属性为`true`，如果为`false`，调用该方法没有任何效果。**该方法只是取消事件对当前元素的默认影响，不会阻止事件的传播。如果要阻止传播，可以使用`stopPropagation()`或`stopImmediatePropagation()`方法。**

`Event.stopPropagation`方法阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上其他的事件监听函数。

`Event.stopImmediatePropagation`方法阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点。也就是说，该方法阻止事件的传播，比`Event.stopPropagation()`更彻底。

`Event.composedPath()`返回一个数组，成员是事件的最底层节点和依次冒泡经过的所有上层节点。

#### 事件模型

##### 事件传播（事件冒泡与事件捕获）

一个事件发生后，会在子元素和父元素之间传播，这个传播分为3个阶段：

- **第一阶段**：从`window`对象传导到目标节点（上层传到底层），称为“捕获阶段”（capture phase）。
- **第二阶段**：在目标节点上触发，称为“目标阶段”（target phase）。
- **第三阶段**：从目标节点传导回`window`对象（从底层传回上层），称为“冒泡阶段”（bubbling phase）。

事件冒泡可以形象地比喻为把一颗石头投入水中，泡泡会一直从水底冒出水面。也就是说，事件会从最内层的元素开始发生，一直向上传播，直到document对象。

网景提出另一种事件流名为**事件捕获**(event capturing)。与事件冒泡相反，事件会从最外层开始发生，直到最具体的元素。

最后采用w3c的折中方案：先捕获后冒泡，形成了事件传播的3个阶段

简单来说，如果父dom和子dom都定义了相同的事件（如点击事件），那么触发子dom的事件时会同时触发父dom上的事件

举例

##### 阻止事件冒泡

```js
$("#div1").mousedown(function(e){
    var e=event||window.event;
    event.stopPropagation();
});
```

不支持冒泡的事件类型：

mouseenter、mouseleave、blur、focus、load、unload、resize

不支持冒泡的事件，可以在捕获阶段实现事件代理

##### 事件委托/事件代理

由于事件传播的存在，事件会在冒泡阶段向上传播到父节点。因此可以把

事件委托，通俗地来讲，就是把一个元素响应事件（click、keydown......）的函数委托到另一个元素；

事件委托会把一个或者一组元素的事件委托到它的父层或者更外层元素上，真正绑定事件的是外层元素，当事件响应到需要绑定的元素上时，会通过事件冒泡机制从而触发它的外层元素的绑定事件上，然后在外层元素上去执行函数。

实例

```html
<ul id="list">
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
  ......
  <li>item n</li>
</ul>
<script>
// 给父层元素绑定事件
document.getElementById('list').addEventListener('click', function (e) {
  // 兼容性处理
  var event = e || window.event;
  var target = event.target || event.srcElement;
  // 判断是否匹配目标元素
  if (target.nodeName.toLocaleLowerCase === 'li') {
    console.log('the content is: ', target.innerHTML);
  }
});
</script>
```

事件委托的优点：

1.减少内存消耗

正常来说，如果我们有一个列表，列表之中有大量的列表项，我们需要在点击列表项的时候响应一个事件；

比较好的方法就是把这个点击事件绑定到他的父层，也就是 `ul` 上，然后在执行事件的时候再去匹配判断目标元素；

所以事件委托可以减少大量的内存消耗，节约效率。

2.可以动态绑定事件

例子中列表项就几个，我们给每个列表项都绑定了事件；

在很多时候，我们需要通过 AJAX 或者用户操作动态的增加或者去除列表项元素，那么在每一次改变的时候都需要重新给新增的元素绑定事件，给即将删去的元素解绑事件；

如果用了事件委托就没有这种麻烦了，因为事件是绑定在父层的，和目标元素的增减是没有关系的，执行到目标元素是在真正响应执行事件函数的过程中去匹配的；

事件委托也是有一定局限性的；

比如 focus、blur 之类的事件本身没有事件冒泡机制，所以无法委托；

mousemove、mouseout 这样的事件，虽然有事件冒泡，但是只能不断通过位置去计算定位，对性能消耗高，因此也是不适合于事件委托的；

对于事件代理(事件委托）来说，在事件捕获或者事件冒泡阶段处理并没有明显的优劣之分，但是由于事件冒泡的事件流模型被所有主流的浏览器兼容，从兼容性角度来说还是建议大家使用事件冒泡模型。

#### 表单事件

input事件

`input`事件当`<input>`、`<select>`、`<textarea>`的值发生变化时触发。对于复选框（`<input type=checkbox>`）或单选框（`<input type=radio>`），用户改变选项时，也会触发这个事件。另外，对于打开`contenteditable`属性的元素，只要值发生变化，也会触发`input`事件。

`input`事件的一个特点，就是会连续触发，比如用户每按下一次按键，就会触发一次`input`事件

该事件跟`change`事件很像，不同之处在于`input`事件在元素的值发生变化后立即发生，而`change`在元素失去焦点时发生，而内容此时可能已经变化多次。也就是说，如果有连续变化，`input`事件会触发多次，而`change`事件只在失去焦点时触发一次

change事件

`change`事件当`<input>`、`<select>`、`<textarea>`的值发生变化时触发。它与`input`事件的最大不同，就是不会连续触发，只有当全部修改完成时才会触发，另一方面`input`事件必然伴随`change`事件。具体来说，分成以下几种情况。

- 激活单选框（radio）或复选框（checkbox）时触发。
- 用户提交时触发。比如，从下列列表（select）完成选择，在日期或文件输入框完成选择。
- 当文本框或`<textarea>`元素的值发生改变，并且丧失焦点时触发。

`select`事件

当在`<input>`、`<textarea>`里面选中文本时触发

选中的文本可以通过`event.target`元素的`selectionDirection`、`selectionEnd`、`selectionStart`和`value`属性拿到。

valid事件

用户提交表单时，如果表单元素的值不满足校验条件，就会触发`invalid`事件

reset、submit事件

这两个事件发生在表单对象`<form>`上，而不是发生在表单的成员上。

`reset`事件当表单重置（所有表单成员变回默认值）时触发。

`submit`事件当表单数据向服务器提交时触发。注意，`submit`事件的发生对象是`<form>`元素，而不是`<button>`元素，因为提交的是表单，而不是按钮

##### 中文输入法输入事件composition

如果一个 INPUT 中，用户使用中文输入法，那么原生的键盘事件就会发生变化，无法获取正确的 keycode 及对应的输入内容。从前端的能力，只能操作浏览器相关的 [API](https://so.csdn.net/so/search?q=API&spm=1001.2101.3001.7020)，不能获取中文输入法的内置 API。查阅资料后，可以使用 composition 事件来监听相关的事件

不同中文输入法的情况可能大同小异（例如搜狗输入法，中文输入时，keycode 就是225，这样无法检测到点击的键）

composition 事件组合分成三个事件：compositionstart、compositionupdate、compositionend，分别对应中文输入法下，开始输入、更新输入、结束输入的事件。

```javascript
// 首先获取INPUT元素，或者全局document元素
const inputElement = document.querySelector('input[type="text"]');

// 当输入区域获取焦点后，点击键盘，会触发 composition 事件组合，通过 event.data 可以获取输入的字符
inputElement.addEventListener('compositionstart', (event) => {
  console.log(`generated characters were: ${event.data}`);
});

```

开始输入首先触发一次 compositionstart 

然后触发一次 compositionupdate，如果继续点击键盘，那么继续触发这个事件

最后，点击空格键输入中文时，触发 compositionend 事件

```javascript
const inputElement = document.querySelector('input[type="text"]');
const log = document.querySelector('.event-log-contents');
const clearLog = document.querySelector('.clear-log');

clearLog.addEventListener('click', () => {
    log.textContent = '';
});

function handleEvent(event) {
    log.textContent = log.textContent + `${event.type}: ${event.data}\n`;
}

inputElement.addEventListener('compositionstart', handleEvent);
inputElement.addEventListener('compositionupdate', handleEvent);
inputElement.addEventListener('compositionend', handleEvent);
```



#### 鼠标事件

鼠标点击事件

- `click`：按下鼠标（通常是按下主按钮）时触发。
- `dblclick`：在同一个元素上双击鼠标时触发。
- `mousedown`：按下鼠标键时触发。
- `mouseup`：释放按下的鼠标键时触发。

鼠标移动事件

`mousemove`：当鼠标在一个节点内部移动时触发。当鼠标持续移动时，该事件会连续触发。为了避免性能问题，建议对该事件的监听函数做一些限定，比如限定一段时间内只能运行一次

`mouseover`事件和`mouseenter`事件，都是鼠标进入一个节点时触发。两者的区别是，`mouseenter`事件只触发一次，而只要鼠标在节点内部移动，`mouseover`事件会在子节点上触发多次

`mouseout`事件和`mouseleave`事件，都是鼠标离开一个节点时触发。两者的区别是，在父元素内部离开一个子元素时，`mouseleave`事件不会触发，而`mouseout`事件会触发





#### 键盘事件

键盘事件由用户击打键盘触发，主要有`keydown`、`keypress`、`keyup`三个事件，它们都继承了`KeyboardEvent`接口。

- `keydown`：按下键盘时触发。
- `keypress`：按下有值的键时触发，即按下 Ctrl、Alt、Shift、Meta 这样无值的键，这个事件不会触发。对于有值的键，按下时先触发`keydown`事件，再触发这个事件。
- `keyup`：松开键盘时触发该事件。

如果用户一直按键不松开，就会连续触发键盘事件，触发的顺序如下。

1. keydown
2. keypress
3. keydown
4. keypress
5. ...（重复以上过程）
6. keyup

`KeyboardEvent`接口用来描述用户与键盘的互动。这个接口继承了`Event`接口，并且定义了自己的实例属性和实例方法

- `KeyboardEvent.altKey`：是否按下 Alt 键
- `KeyboardEvent.ctrlKey`：是否按下 Ctrl 键
- `KeyboardEvent.metaKey`：是否按下 meta 键（Mac 系统是一个四瓣的小花，Windows 系统是 windows 键）
- `KeyboardEvent.shiftKey`：是否按下 Shift 键

#### 进度事件

进度事件用来描述资源加载的进度，主要由 AJAX 请求、`<img>`、`<audio>`、`<video>`、`<style>`、`<link>`等外部资源的加载触发，继承了`ProgressEvent`接口。它主要包含以下几种事件。

- `abort`：外部资源中止加载时（比如用户取消）触发。如果发生错误导致中止，不会触发该事件。
- `error`：由于错误导致外部资源无法加载时触发。
- `load`：外部资源加载成功时触发。
- `loadstart`：外部资源开始加载时触发。
- `loadend`：外部资源停止加载时触发，发生顺序排在`error`、`abort`、`load`等事件的后面。
- `progress`：外部资源加载过程中不断触发。
- `timeout`：加载超时时触发。

`ProgressEvent`接口主要用来描述外部资源加载的进度，比如 AJAX 加载、`<img>`、`<video>`、`<style>`、`<link>`等外部资源加载。进度相关的事件都继承了这个接口

`ProgressEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件的类型，这个参数是必须的。第二个参数是一个配置对象，表示事件的属性，该参数可选。配置对象除了可以使用`Event`接口的配置属性，还可以使用下面的属性，所有这些属性都是可选的。

- `lengthComputable`：布尔值，表示加载的总量是否可以计算，默认是`false`。
- `loaded`：整数，表示已经加载的量，默认是`0`。
- `total`：整数，表示需要加载的总量，默认是`0`。

`ProgressEvent`具有对应的实例属性。

- `ProgressEvent.lengthComputable`
- `ProgressEvent.loaded`
- `ProgressEvent.total`

#### 拖动事件

拖拉（drag）指的是，用户在某个对象上按下鼠标键不放，拖动它到另一个位置，然后释放鼠标键，将该对象放在那里。

拖拉的对象有好几种，包括元素节点、图片、链接、选中的文字等等。在网页中，除了元素节点默认不可以拖拉，其他（图片、链接、选中的文字）都可以直接拖拉。为了让元素节点可拖拉，可以将该节点的`draggable`属性设为`true`。

`draggable`属性可用于任何元素节点，但是图片（`<img>`）和链接（`<a>`）不加这个属性，就可以拖拉。对于它们，用到这个属性的时候，往往是将其设为`false`，防止拖拉这两种元素。

当元素节点或选中的文本被拖拉时，就会持续触发拖拉事件，包括以下一些事件。

- `drag`：拖拉过程中，在被拖拉的节点上持续触发（相隔几百毫秒）。
- `dragstart`：用户开始拖拉时，在被拖拉的节点上触发，该事件的`target`属性是被拖拉的节点。通常应该在这个事件的监听函数中，指定拖拉的数据。
- `dragend`：拖拉结束时（释放鼠标键或按下 ESC 键）在被拖拉的节点上触发，该事件的`target`属性是被拖拉的节点。它与`dragstart`事件，在同一个节点上触发。不管拖拉是否跨窗口，或者中途被取消，`dragend`事件总是会触发的。
- `dragenter`：拖拉进入当前节点时，在当前节点上触发一次，该事件的`target`属性是当前节点。通常应该在这个事件的监听函数中，指定是否允许在当前节点放下（drop）拖拉的数据。如果当前节点没有该事件的监听函数，或者监听函数不执行任何操作，就意味着不允许在当前节点放下数据。在视觉上显示拖拉进入当前节点，也是在这个事件的监听函数中设置。
- `dragover`：拖拉到当前节点上方时，在当前节点上持续触发（相隔几百毫秒），该事件的`target`属性是当前节点。该事件与`dragenter`事件的区别是，`dragenter`事件在进入该节点时触发，然后只要没有离开这个节点，`dragover`事件会持续触发。
- `dragleave`：拖拉操作离开当前节点范围时，在当前节点上触发，该事件的`target`属性是当前节点。如果要在视觉上显示拖拉离开操作当前节点，就在这个事件的监听函数中设置。
- `drop`：被拖拉的节点或选中的文本，释放到目标节点时，在目标节点上触发。注意，如果当前节点不允许`drop`，即使在该节点上方松开鼠标键，也不会触发该事件。如果用户按下 ESC 键，取消这个操作，也不会触发该事件。该事件的监听函数负责取出拖拉数据，并进行相关处理。





`DataTransfer.setData()`方法用来设置拖拉事件所带有的数据。该方法没有返回值。

`DataTransfer.getData()`方法接受一个字符串（表示数据类型）作为参数，返回事件所带的指定类型的数据（通常是用`setData`方法添加的数据）。如果指定类型的数据不存在，则返回空字符串。通常只有`drop`事件触发后，才能取出数据。

`DataTransfer.clearData()`方法接受一个字符串（表示数据类型）作为参数，删除事件所带的指定类型的数据。如果没有指定类型，则删除所有数据。如果指定类型不存在，则调用该方法不会产生任何效果。

`DataTransfer.setDragImage()`拖动过程中（`dragstart`事件触发后），浏览器会显示一张图片跟随鼠标一起移动，表示被拖动的节点。这张图片是自动创造的，通常显示为被拖动节点的外观，不需要自己动手设置。

`DataTransfer.setDragImage()`方法可以自定义这张图片。它接受三个参数。第一个是`<img>`节点或者`<canvas>`节点，如果省略或为`null`，则使用被拖动的节点的外观；第二个和第三个参数为鼠标相对于该图片左上角的横坐标和纵坐标。

#### 其他事件

##### 资源事件

beforeunload事件

`beforeunload`事件在窗口、文档、各种资源将要卸载前触发。它可以用来防止用户不小心卸载资源。

如果该事件对象的`returnValue`属性是一个非空字符串，那么浏览器就会弹出一个对话框，询问用户是否要卸载该资源。但是，用户指定的字符串可能无法显示，浏览器会展示预定义的字符串。如果用户点击“取消”按钮，资源就不会卸载。

```javascript
window.addEventListener('beforeunload', function (event) {
  event.returnValue = '你确定离开吗？';
});
```

unload事件

`unload`事件在窗口关闭或者`document`对象将要卸载时触发。它的触发顺序排在`beforeunload`、`pagehide`事件后面。

`unload`事件发生时，文档处于一个特殊状态。所有资源依然存在，但是对用户来说都不可见，UI 互动全部无效。这个事件是无法取消的，即使在监听函数里面抛出错误，也不能停止文档的卸载。

```javascript
window.addEventListener('unload', function(event) {
  console.log('文档将要卸载');
});
```

手机上，浏览器或系统可能会直接丢弃网页，这时该事件根本不会发生。而且跟`beforeunload`事件一样，一旦使用了`unload`事件，浏览器就不会缓存当前网页，理由同上。因此，任何情况下都不应该依赖这个事件，指定网页卸载时要执行的代码，可以考虑完全不使用这个事件。

该事件可以用`pagehide`代替。

Load  Error事件

`load`事件在页面或某个资源加载成功时触发。注意，页面或资源从浏览器缓存加载，并不会触发`load`事件。

`error`事件是在页面或资源加载失败时触发。`abort`事件在用户取消加载时触发。

```javascript
window.addEventListener('load', function(event) {
  console.log('所有资源都加载完成');
});
```

这三个事件实际上属于进度事件，不仅发生在`document`对象，还发生在各种外部资源上面。浏览网页就是一个加载各种资源的过程，图像（image）、样式表（style sheet）、脚本（script）、视频（video）、音频（audio）、Ajax请求（XMLHttpRequest）等等。这些资源和`document`对象、`window`对象、XMLHttpRequestUpload 对象，都会触发`load`事件和`error`事件。

最后，页面的`load`事件也可以用`pageshow`事件代替。

##### 会话历史事件

pageshow 事件，pagehide 事件

默认情况下，浏览器会在当前会话（session）缓存页面，当用户点击“前进/后退”按钮时，浏览器就会从缓存中加载页面。

`pageshow`事件在页面加载时触发，包括第一次加载和从缓存加载两种情况。如果要指定页面每次加载（不管是不是从浏览器缓存）时都运行的代码，可以放在这个事件的监听函数。

第一次加载时，它的触发顺序排在`load`事件后面。从缓存加载时，`load`事件不会触发，因为网页在缓存中的样子通常是`load`事件的监听函数运行后的样子，所以不必重复执行。同理，如果是从缓存中加载页面，网页内初始化的 JavaScript 脚本（比如 DOMContentLoaded 事件的监听函数）也不会执行。

`pageshow`事件有一个`persisted`属性，返回一个布尔值。页面第一次加载时，这个属性是`false`；当页面从缓存加载时，这个属性是`true`。

```javascript
window.addEventListener('pageshow', function(event) {
  console.log('pageshow: ', event);
});

window.addEventListener('pageshow', function(event){
  if (event.persisted) {
    // ...
  }
});
```

`pagehide`事件与`pageshow`事件类似，当用户通过“前进/后退”按钮，离开当前页面时触发。它与 unload 事件的区别在于，如果在 window 对象上定义`unload`事件的监听函数之后，页面不会保存在缓存中，而使用`pagehide`事件，页面会保存在缓存中。

`pagehide`事件实例也有一个`persisted`属性，将这个属性设为`true`，就表示页面要保存在缓存中；设为`false`，表示网页不保存在缓存中，这时如果设置了unload 事件的监听函数，该函数将在 pagehide 事件后立即运行。

如果页面包含`<frame>`或`<iframe>`元素，则`<frame>`页面的`pageshow`事件和`pagehide`事件，都会在主页面之前触发。

注意，这两个事件只在浏览器的`history`对象发生变化时触发，跟网页是否可见没有关系。

popState事件

`popstate`事件在浏览器的`history`对象的当前记录发生显式切换时触发。注意，调用`history.pushState()`或`history.replaceState()`，并不会触发`popstate`事件。该事件只在用户在`history`记录之间显式切换时触发，比如鼠标点击“后退/前进”按钮，或者在脚本中调用`history.back()`、`history.forward()`、`history.go()`时触发。

该事件对象有一个`state`属性，保存`history.pushState`方法和`history.replaceState`方法为当前记录添加的`state`对象

```javascript
window.onpopstate = function (event) {
  console.log('state: ' + event.state);
};
history.pushState({page: 1}, 'title 1', '?page=1');
history.pushState({page: 2}, 'title 2', '?page=2');
history.replaceState({page: 3}, 'title 3', '?page=3');
history.back(); // state: {"page":1}
history.back(); // state: null
history.go(2);  // state: {"page":3}
```

上面代码中，`pushState`方法向`history`添加了两条记录，然后`replaceState`方法替换掉当前记录。因此，连续两次`back`方法，会让当前条目退回到原始网址，它没有附带`state`对象，所以事件的`state`属性为`null`，然后前进两条记录，又回到`replaceState`方法添加的记录。

浏览器对于页面首次加载，是否触发`popstate`事件，处理不一样，Firefox 不触发该事件。

hashchange事件

`hashchange`事件在 URL 的 hash 部分（即`#`号后面的部分，包括`#`号）发生变化时触发。该事件一般在`window`对象上监听。

`hashchange`的事件实例具有两个特有属性：`oldURL`属性和`newURL`属性，分别表示变化前后的完整 URL。

```javascript
// URL 是 http://www.example.com/
window.addEventListener('hashchange', myFunction);

function myFunction(e) {
  console.log(e.oldURL);
  console.log(e.newURL);
}

location.hash = 'part2';
// http://www.example.com/
// http://www.example.com/#part2
```

##### 网页状态事件

DOMContentLoaded 事件

网页下载并解析完成以后，浏览器就会在`document`对象上触发 DOMContentLoaded 事件。这时，仅仅完成了网页的解析（整张页面的 DOM 生成了），所有外部资源（样式表、脚本、iframe 等等）可能还没有下载结束。也就是说，这个事件比`load`事件，发生时间早得多。

注意，网页的 JavaScript 脚本是同步执行的，脚本一旦发生堵塞，将推迟触发`DOMContentLoaded`事件。

```javascript
document.addEventListener('DOMContentLoaded', function (event) {
  console.log('DOM生成');
});

// 这段代码会推迟触发 DOMContentLoaded 事件
for(var i = 0; i < 1000000000; i++) {
  // ...
}
```

readystatechange事件

`readystatechange`事件当 Document 对象和 XMLHttpRequest 对象的`readyState`属性发生变化时触发。`document.readyState`有三个可能的值：`loading`（网页正在加载）、`interactive`（网页已经解析完成，但是外部资源仍然处在加载状态）和`complete`（网页和所有外部资源已经结束加载，`load`事件即将触发）

```javascript
document.onreadystatechange = function () {
  if (document.readyState === 'interactive') {
    // ...
  }
}
```

这个事件可以看作`DOMContentLoaded`事件的另一种实现方法

##### 窗口事件

scroll事件

`scroll`事件在文档或文档元素滚动时触发，主要出现在用户拖动滚动条。

该事件会连续地大量触发，所以它的监听函数之中不应该有非常耗费计算的操作。推荐的做法是使用`requestAnimationFrame`或`setTimeout`控制该事件的触发频率，然后可以结合`customEvent`抛出一个新事件

```javascript
(function () {
  var throttle = function (type, name, obj) {
    var obj = obj || window;
    var running = false;
    var func = function () {
      if (running) { return; }
      running = true;
      requestAnimationFrame(function() {
        obj.dispatchEvent(new CustomEvent(name));
        running = false;
      });
    };
    obj.addEventListener(type, func);
  };

  // 将 scroll 事件转为 optimizedScroll 事件
  throttle('scroll', 'optimizedScroll');
})();

window.addEventListener('optimizedScroll', function() {
  console.log('Resource conscious scroll callback!');
});

function throttle(fn, wait) {
  var time = Date.now();
  return function() {
    if ((time + wait - Date.now()) < 0) {
      fn();
      time = Date.now();
    }
  }
}

window.addEventListener('scroll', throttle(callback, 1000));
```

`throttle`与它区别在于，`throttle`是“节流”，确保一段时间内只执行一次，而`debounce`是“防抖”，要连续操作结束后再执行。以网页滚动为例，`debounce`要等到用户停止滚动后才执行，`throttle`则是如果用户一直在滚动网页，那么在滚动过程中还是会执行

resize事件

`resize`事件在改变浏览器窗口大小时触发，主要发生在`window`对象上面

该事件也会连续地大量触发，所以最好像上面的`scroll`事件一样，通过`throttle`函数控制事件触发频率。

```javascript
var resizeMethod = function () {
  if (document.body.clientWidth < 768) {
    console.log('移动设备的视口');
  }
};

window.addEventListener('resize', resizeMethod, true);
```

fullscreenchange 事件，fullscreenerror 事件

`fullscreenchange`事件在进入或退出全屏状态时触发，该事件发生在`document`对象上面

`fullscreenerror`事件在浏览器无法切换到全屏状态时触发。

```javascript
document.addEventListener('fullscreenchange', function (event) {
  console.log(document.fullscreenElement);
});
```

##### 剪贴板事件

以下三个事件属于剪贴板操作的相关事件。

- `cut`：将选中的内容从文档中移除，加入剪贴板时触发。
- `copy`：进行复制动作时触发。
- `paste`：剪贴板内容粘贴到文档后触发。

如果希望禁止输入框的粘贴事件，可以使用下面的代码。

```javascript
inputElement.addEventListener('paste', e => e.preventDefault());
```

上面的代码使得用户无法在`<input>`输入框里面粘贴内容。

`cut`、`copy`、`paste`这三个事件的事件对象都是`ClipboardEvent`接口的实例。`ClipboardEvent`有一个实例属性`clipboardData`，是一个 DataTransfer 对象，存放剪贴的数据。

```javascript
document.addEventListener('copy', function (e) {
  e.clipboardData.setData('text/plain', 'Hello, world!');
  e.clipboardData.setData('text/html', '<b>Hello, world!</b>');
  e.preventDefault();
});
```

##### 焦点事件

焦点事件发生在元素节点和`document`对象上面，与获得或失去焦点相关。它主要包括以下四个事件。

- `focus`：元素节点获得焦点后触发，该事件不会冒泡。
- `blur`：元素节点失去焦点后触发，该事件不会冒泡。
- `focusin`：元素节点将要获得焦点时触发，发生在`focus`事件之前。该事件会冒泡。
- `focusout`：元素节点将要失去焦点时触发，发生在`blur`事件之前。该事件会冒泡。

这四个事件的事件对象都继承了`FocusEvent`接口。`FocusEvent`实例具有以下属性。

- `FocusEvent.target`：事件的目标节点。
- `FocusEvent.relatedTarget`：对于`focusin`事件，返回失去焦点的节点；对于`focusout`事件，返回将要接受焦点的节点；对于`focus`和`blur`事件，返回`null`。

由于`focus`和`blur`事件不会冒泡，只能在捕获阶段触发，所以`addEventListener`方法的第三个参数需要设为`true`。

```javascript
form.addEventListener('focus', function (event) {
  event.target.style.background = 'pink';
}, true);

form.addEventListener('blur', function (event) {
  event.target.style.background = '';
}, true);
```

customEvent接口

CustomEvent 接口用于生成自定义的事件实例。那些浏览器预定义的事件，虽然可以手动生成，但是往往不能在事件上绑定数据。如果需要在触发事件的同时，传入指定的数据，就可以使用 CustomEvent 接口生成的自定义事件对象。

浏览器原生提供`CustomEvent()`构造函数，用来生成 CustomEvent 事件实例。

`CustomEvent()`构造函数接受两个参数。第一个参数是字符串，表示事件的名字，这是必须的。第二个参数是事件的配置对象，这个参数是可选的。`CustomEvent`的配置对象除了接受 Event 事件的配置属性，只有一个自己的属性。

- `detail`：表示事件的附带数据，默认为`null`。

```javascript
var event = new CustomEvent('build', { 'detail': 'hello' });

function eventHandler(e) {
  console.log(e.detail);
}

document.body.addEventListener('build', function (e) {
  console.log(e.detail);
});

document.body.dispatchEvent(event);
```

上面的代码中，我们手动定义了`build`事件。该事件触发后，会被监听到，从而输出该事件实例的`detail`属性（即字符串`hello`）

### 全局事件

指定事件的回调函数，推荐使用的方法是元素的`addEventListener`方法。

除了之外，还有一种方法可以直接指定事件的回调函数。

这个接口是由`GlobalEventHandlers`接口提供的。它的优点是使用比较方便，缺点是只能为每个事件指定一个回调函数，并且无法指定事件触发的阶段（捕获阶段还是冒泡阶段）。

`HTMLElement`、`Document`和`Window`都继承了这个接口，也就是说，各种 HTML 元素、`document`对象、`window`对象上面都可以使用`GlobalEventHandlers`接口提供的属性。



