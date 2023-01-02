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

## 宿主对象

#### Window

属性

`window.name`属性是一个字符串，表示当前浏览器窗口的名字。窗口不一定需要名字，这个属性主要配合超链接和表单的`target`属性使用。

`window.closed`属性返回一个布尔值，表示窗口是否关闭。

`window.opener`属性表示打开当前窗口的父窗口。如果当前窗口没有父窗口（即直接在地址栏输入打开），则返回`null`。

`window.frames`属性返回一个类似数组的对象，成员为页面内所有框架窗口，包括`frame`元素和`iframe`元素。`window.frames[0]`表示页面中第一个框架窗口。

`window.length`属性返回当前网页包含的框架总数。如果当前网页不包含`frame`和`iframe`元素，那么`window.length`就返回`0`。

`window.frameElement`属性主要用于当前窗口嵌在另一个网页的情况（嵌入`<object>`、`<iframe>`或`<embed>`元素），返回当前窗口所在的那个元素节点。如果当前窗口是顶层窗口，或者所嵌入的那个网页不是同源的，该属性返回`null`。

`window.top`属性指向最顶层窗口，主要用于在框架窗口（frame）里面获取顶层窗口。

`window.parent`属性指向父窗口。如果当前窗口没有父窗口，`window.parent`指向自身。

`window.devicePixelRatio`属性返回一个数值，表示一个 CSS 像素的大小与一个物理像素的大小之间的比率。也就是说，它表示一个 CSS 像素由多少个物理像素组成。它可以用于判断用户的显示环境，如果这个比率较大，就表示用户正在使用高清屏幕，因此可以显示较大像素的图片。

`window.screenX`和`window.screenY`属性，返回浏览器窗口左上角相对于当前屏幕左上角的水平距离和垂直距离（单位像素）。这两个属性只读。

`window.innerHeight`和`window.innerWidth`属性，返回网页在当前窗口中可见部分的高度和宽度，即“视口”（viewport）的大小（单位像素）。这两个属性只读。

用户放大网页的时候（比如将网页从100%的大小放大为200%），这两个属性会变小。因为这时网页的像素大小不变（比如宽度还是960像素），只是每个像素占据的屏幕空间变大了，因为可见部分（视口）就变小了。

`window.outerHeight`和`window.outerWidth`属性返回浏览器窗口的高度和宽度，包括浏览器菜单和边框（单位像素）。这两个属性只读。

`window.scrollX`属性返回页面的水平滚动距离，`window.scrollY`属性返回页面的垂直滚动距离，单位都为像素。这两个属性只读。

- `window.locationbar`：地址栏对象
- `window.menubar`：菜单栏对象
- `window.scrollbars`：窗口的滚动条对象
- `window.toolbar`：工具栏对象
- `window.statusbar`：状态栏对象
- `window.personalbar`：用户安装的个人工具栏对象

window实例方法
`window.alert('Hello World')`;//弹出对话框，只有确定按钮，用于提醒用户信息
`var result = confirm('你最近好吗？');`//弹出的对话框，除了提示信息之外，只有“确定”和“取消”,返回布尔值
`var result = window.prompt()//`也是弹出对话框，但是提示框下面有输入框，并有“确定”和“取消”两个按钮，返回布尔值
`window.open(url, windowName, [windowFeatures])`新建另一个浏览器窗口，url为地址栏，
`window.close()`window方法,用于关闭当前窗口，

`window.stop()`方法完全等同于单击浏览器的停止按钮，会停止加载图像、视频等正在或等待加载的对象。

`window.moveTo(100, 200)`

`window.moveBy(25, 50)`

`window.focus()`激活窗口，使其获得焦点，出现在其他窗口的前面。

`window.blur()`方法将焦点从窗口移除。

`window.getSelection`方法返回一个`Selection`对象，表示用户现在选中的文本。

`window.resizeTo()`方法用于缩放窗口到指定大小。

`window.resizeBy()`方法用于缩放窗口。它与`window.resizeTo()`的区别是，它按照相对的量缩放，`window.resizeTo()`需要给出缩放后的绝对大小。

`window.scrollTo`方法用于将文档滚动到指定位置。它接受两个参数，表示滚动后位于窗口左上角的页面坐标。

`window.scrollBy()`方法用于将网页滚动指定距离（单位像素）。它接受两个参数：水平向右滚动的像素，垂直向下滚动的像素。

`window.print`方法会跳出打印对话框，与用户点击菜单里面的“打印”命令效果相同。

`window.getSelection`方法返回一个`Selection`对象，表示用户现在选中的文本。

`window.getComputedStyle()`方法接受一个元素节点作为参数，返回一个包含该元素的最终样式信息的对象

`window.matchMedia()`方法用来检查 CSS 的`mediaQuery`语句

##### requestAnimationFrame()与requestIdleCallback()事件

`window.requestAnimationFrame()`方法跟`setTimeout`类似，都是推迟某个函数的执行。不同之处在于，`setTimeout`必须指定推迟的时间，`window.requestAnimationFrame()`则是推迟到浏览器下一次重流时执行，执行完才会进行下一次重绘。重绘通常是 16ms 执行一次，不过浏览器会自动调节这个速率，比如网页切换到后台 Tab 页时，`requestAnimationFrame()`会暂停执行。

requestAnimationFrame采用系统时间间隔，保持最佳绘制效率，不会因为间隔时间过短，造成过度绘制，增加开销；也不会因为间隔时间太长，使用动画卡顿不流畅，让各种网页动画效果能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果。

如果某个函数会改变网页的布局，一般就放在`window.requestAnimationFrame()`里面执行，这样可以节省系统资源，使得网页效果更加平滑。因为慢速设备会用较慢的速率重流和重绘，而速度更快的设备会有更快的速率。requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率
在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的CPU、GPU和内存使用量
requestAnimationFrame是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了CPU开销

`window.requestIdleCallback()`跟`setTimeout`类似，也是将某个函数推迟执行，但是它保证将回调函数推迟到系统资源空闲时执行。也就是说，如果某个任务不是很关键，就可以使用`window.requestIdleCallback()`将其推迟执行，以保证网页性能。

它跟`window.requestAnimationFrame()`的区别在于，后者指定回调函数在下一次浏览器重排时执行，问题在于下一次重排时，系统资源未必空闲，不一定能保证在16毫秒之内完成； `window.requestIdleCallback()`可以保证回调函数在系统资源空闲时执行。

##### 获取当前动画fps

requestAnimationFrame的回调函数执行次数通常与浏览器屏幕刷新次数相匹配，而利用这个API实现动画的原理就是回调函数内再次调用requestAnimationFrame，所以页面不断重绘时，然后检测1秒内requestAnimationFrame调用的次数，就是当前的FPS

```html
<script>
  let num = 0
  let height = 0
  let frame = 0
  window.onload = () => {
    let animationHeight = () => {
      document.getElementById('div').style.height = (++height) + 'px'
      if (height < 1000) {
        frame++
        requestAnimationFrame(animationHeight)
      }
    }
    // 每秒钟输出当前帧数
    setInterval(() => {
      console.log(frame)
      frame = 0
    }, 1000)
    requestAnimationFrame(animationHeight)
  }

</script>
```

事件

`load`事件发生在文档在浏览器窗口加载完毕时。`window.onload`属性可以指定这个事件的回调函数。

//浏览器脚本发生错误时，会触发window对象的error事件。通过window.onerror属性对该事件指定回调函数。

window.onerror = function(message,error){
  console.log("出错了")
}

Window对象的事件监听属性

```js
window.onafterprint：afterprint事件的监听函数。
window.onbeforeprint：beforeprint事件的监听函数。
window.onbeforeunload：beforeunload事件的监听函数。
window.onhashchange：hashchange事件的监听函数。
window.onlanguagechange: languagechange的监听函数。
window.onmessage：message事件的监听函数。
window.onmessageerror：MessageError事件的监听函数。
window.onoffline：offline事件的监听函数。
window.ononline：online事件的监听函数。
window.onpagehide：pagehide事件的监听函数。
window.onpageshow：pageshow事件的监听函数。
window.onpopstate：popstate事件的监听函数。
window.onstorage：storage事件的监听函数。
window.onunhandledrejection：未处理的 Promise 对象的reject事件的监听函数。
window.onunload：unload事件的监听函数。
```

##### window.onload与DOMcontentloaded、jquery中document.ready的区别

DOM解析的完整过程：

1. 解析HTML结构。
2. 加载外部脚本和样式表文件。
3. 解析并执行脚本代码。//js之类的
4. DOM树构建完成。//DOMContentLoaded
5. 加载图片等外部文件。
6. 页面加载完毕。//load

在第4步的时候`DOMContentLoaded`事件会被触发,也就是jquery的document.ready()事件。
在第6步的时候`load`事件会被触发。

区分的必要性

开发中我们经常需要给一些元素的事件绑定处理函数。但问题是，如果那个元素还没有加载到页面上，但是绑定事件已经执行完了，是没有效果的。这两个事件大致就是用来避免这样一种情况，将绑定的函数放在这两个事件的回调中，保证能在页面的某些元素加载完毕之后再绑定事件的函数。
 当然DOMContentLoaded机制更加合理，因为我们可以容忍图片，flash延迟加载，却不可以容忍看见内容后页面不可交互。



#### Location

Location是浏览器提供的原生对象，提供URL相关的信息和操作方法。

```js
document.location.href// http://user:passwd@www.example.com:4097/path/a.html?x=111#part1
document.location.protocol//当前 URL 的协议，包括冒号（:），"http:"
document.location.host//主机。如果端口不是协议默认的80和433，则还会包括冒号（:）和端口。"www.example.com:4097"
document.location.hostname//主机名，不包括端口，"www.example.com"
document.location.port//端口号，"4097"
document.location.pathname// URL 的路径部分，从根路径/开始。"/path/a.html"
document.location.search// "?x=111"
document.location.hash// "#part1"
document.location.username//域名前面的用户名，"user"
document.location.password//域名前面的密码，"passwd"
document.location.origin//URL 的协议、主机名和端口，"http://user:passwd@www.example.com:4097"
```

方法

```js
document.location.assign('http://www.example.com')//跳转到新的网址，可回退
document.location.replace('http://www.example.com')//跳转到新网址，不可回退
window.location.reload(true);// 向服务器重新请求当前网址
location.toString()//返回整个 URL 字符串，相当于读取Location.href属性。
```

#### History

`window.history`属性指向 History 对象，它表示当前窗口的浏览历史。

属性

`History.length`：当前窗口访问过的网址数量（包括当前网页）

`History.state`：History 堆栈最上层的状态值（详见下文）

方法

`History.back()`：移动到上一个网址，等同于点击浏览器的后退键。对于第一个访问的网址，该方法无效果。

`History.forward()`：移动到下一个网址，等同于点击浏览器的前进键。对于最后一个访问的网址，该方法无效果。

`History.go()`：接受一个整数作为参数，以当前网址为基准，移动到参数指定的网址，比如`go(1)`相当于`forward()`，`go(-1)`相当于`back()`。

`History.pushState(state, title, url)`方法用于在历史中添加一条记录。

`History.replaceState()`方法用来修改 History 对象的当前记录

#### Navigator 

指向浏览器和系统信息

```javascript
//属性
navigator.userAgent();//返回浏览器的useragent字符串，表示浏览器的厂商和版本信息
Navigator.platform//属性,返回用户的操作系统信息，比如MacIntel、Win32、Linux x86_64等。
navigator.cookieEnabled//返回一个布尔值，表示浏览器的 Cookie 功能是否打开。
//方法
navigator.javaEnabled()//方法，返回一个布尔值，表示浏览器是否能运行 Java Applet 小程序。
navigator.language//属性，返回一个字符串，表示浏览器的首选语言
Navigator.languages//属性，返回一个数组，表示用户可以接受的语言。

Navigator.geolocation//属性，返回一个 Geolocation 对象，包含用户地理位置的信息。该 API 只有在 HTTPS 协议下可用，
Geolocation.getCurrentPosition()//得到用户的当前位置
Geolocation.watchPosition()//监听用户位置变化
Geolocation.clearWatch()//取消watchPosition()方法指定的监听函数
```

window.screen表示当前显示的窗口，返回设备的显示信息

```js
Screen.height：浏览器窗口所在的屏幕的高度（单位像素）。
Screen.width：浏览器窗口所在的屏幕的宽度（单位像素）
Screen.availHeight：浏览器窗口可用的屏幕高度（单位像素）
Screen.availWidth：浏览器窗口可用的屏幕宽度（单位像素）
Screen.pixelDepth：整数，表示屏幕的色彩位数，比如24表示屏幕提供24位色彩。
Screen.colorDepth：Screen.pixelDepth的别名。严格地说，colorDepth 表示应用程序的颜色深度，pixelDepth 表示屏幕的颜色深度，绝大多数情况下，它们都是同一件事。
Screen.orientation：返回一个对象，表示屏幕的方向。该对象的type属性是一个字符串，表示屏幕的具体方向，landscape-primary表示横放，landscape-secondary表示颠倒的横放，portrait-primary表示竖放，portrait-secondary
```

#### XMLHttpRequest 对象

1999年，微软公司发布 IE 浏览器5.0版，第一次引入新功能：允许 JavaScript 脚本向服务器发起 HTTP 请求。2005年2月，AJAX 这个词第一次正式提出，它是Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。

后来，AJAX 这个词就成为 JavaScript 脚本发起 HTTP 通信的代名词，也就是说，只要用脚本发起通信，就可以叫做 AJAX 通信。

AJAX 通过原生的`XMLHttpRequest`对象发出 HTTP 请求，得到服务器返回的数据后，再进行处理。现在，服务器返回的都是 JSON 格式的数据，XML 格式已经过时了，但是 AJAX 这个名字已经成了一个通用名词，字面含义已经消失了。

ajax请求的步骤

1. 创建 XMLHttpRequest 实例
2. 发出 HTTP 请求
3. 接收服务器传回的数据
4. 更新网页数据

ajax支持多种协议（ftp、file等），可以发送任何格式的数据。

XMLHttpRequest对象的属性

`XMLHttpRequest.readyState`返回一个整数，表示实例对象的当前状态。该属性只读，返回以下值：

0:XMLHttpRequest 实例已经创建，但是没调用open方法

1:open方法已经调用，但没有调用send方法发起请求。此时仍然可以使用实例的setRequestHeader()方法设定HTTP请求的头信息

2:表示实例的send方法已经调用，服务器返回的头信息和状态码已经收到

3:表示正在接收服务器传来的数据体，此时如果实例的responseType属性等于text或者空字符串，responseText属性就会包含已经收到的部分信息

4:表示服务器返回的数据已经完全接收，或者本次接收已经失败

`XMLHttpRequest.onreadystatechange`属性指向一个监听函数，readystatechange事件发生时就会执行这个函数。此外，如果实例执行abort方法，也会触发该属性

**`XMLHttpRequest.response`**属性表示服务器返回的数据体。它可能是任何数据类型，比如字符串、二进制对象、对象等等。

**`XMLHttpRequest.responseType`**属性是一个字符串，表示服务器返回数据的类型，这个属性是可写的。设置这个属性的值可以告诉浏览器如何解读返回的数据。如果responseType设为空字符串，就是默认值text

responseType也可以是以下值：

Array buffer:ArrayBuffer对象，表示服务器返回二进制数组

blob：Blob对象，表示服务器返回二进制对象

document：Document对象，表示服务器返回一个文档对象

json：JSON对象

text：字符串

**`XMLHttpRequest.responseText`**属性返回从服务器接收到的字符串，该属性为只读。只有 HTTP 请求完成接收以后，该属性才会包含完整的数据。

**`XMLHttpRequest.responseURL`**属性是字符串，表示发送数据的服务器的网址。

`XMLHttpRequest.status`属性返回一个整数，表示服务器回应的 HTTP 状态码。

XMLHttpRequest 对象事件监听属性：

XMLHttpRequest 对象可以对以下事件指定监听函数。

- XMLHttpRequest.onloadstart：loadstart 事件（HTTP 请求发出）的监听函数
- XMLHttpRequest.onprogress：progress事件（正在发送和加载数据）的监听函数
- XMLHttpRequest.onabort：abort 事件（请求中止，比如用户调用了`abort()`方法）的监听函数
- XMLHttpRequest.onerror：error 事件（请求失败）的监听函数
- XMLHttpRequest.onload：load 事件（请求成功完成）的监听函数
- XMLHttpRequest.ontimeout：timeout 事件（用户指定的时限超过了，请求还未完成）的监听函数
- XMLHttpRequest.onloadend：loadend 事件（请求完成，不管成功或失败）的监听函数

XMLHttpRequest对象的方法

`XMLHttpRequest.open()`方法用于指定 HTTP 请求的参数，或者说初始化 XMLHttpRequest 实例对象。它一共可以接受五个参数。method：表示 HTTP 动词方法，比如GET、POST、PUT、DELETE、HEAD等。
url: 表示请求发送目标 URL。
async: 布尔值，表示请求是否为异步，默认为true。如果设为false，则send()方法只有等到收到服务器返回了结果，才会进行下一步操作。该参数可选。由于同步 AJAX 请求会造成浏览器失去响应，许多浏览器已经禁止在主线程使用，只允许 Worker 里面使用。所以，这个参数轻易不应该设为false。
user：表示用于认证的用户名，默认为空字符串。该参数可选。
password：表示用于认证的密码，默认为空字符串。该参数可选。

`XMLHttpRequest.send()`方法用于实际发出 HTTP 请求。它的参数是可选的，如果不带参数，就表示 HTTP 请求只有一个 URL，没有数据体，典型例子就是 GET 请求；如果带有参数，就表示除了头信息，还带有包含具体数据的信息体，典型例子就是 POST 请求。

`XMLHttpRequest.setRequestHeader()`方法用于设置浏览器发送的 HTTP 请求的头信息。该方法接受两个参数。第一个参数是字符串，表示头信息的字段名，第二个参数是字段值。

`XMLHttpRequest.getResponseHeader()`方法返回 HTTP 头信息指定字段的值，如果还没有收到服务器回应或者指定字段不存在，返回null

`XMLHttpRequest.getAllResponseHeaders()`方法返回一个字符串，表示服务器发来的所有 HTTP 头信息。格式为字符串，每个头信息之间使用`CRLF`分隔（回车+换行），如果没有收到服务器回应，该属性为`null`。如果发生网络错误，该属性为空字符串。

`XMLHttpRequest.abort()`方法用来终止已经发出的 HTTP 请求。调用这个方法以后，`readyState`属性变为`4`，`status`属性变为`0`。

XMLHttpRequest 对象的事件

readyStateChange 事件

`readyState`属性的值发生改变，就会触发 readyStateChange 事件。

我们可以通过`onReadyStateChange`属性，指定这个事件的监听函数，对不同状态进行不同处理。尤其是当状态变为`4`的时候，表示通信成功，这时回调函数就可以处理服务器传送回来的数据。

progress 事件

XMLHttpRequest 实例对象本身和实例的`upload`属性，都有一个`progress`事件，会不断返回上传的进度。

load 事件表示服务器传来的数据接收完毕，error 事件表示请求出错，abort 事件表示请求被中断（比如用户取消请求）。

loadend事件

`abort`、`load`和`error`这三个事件，会伴随一个`loadend`事件，表示请求结束，但不知道其是否成功。

#### fetch

`fetch()`的功能与 XMLHttpRequest 基本相同，但有三个主要的差异。

（1）`fetch()`使用 Promise，不使用回调函数，因此大大简化了写法，写起来更简洁。

（2）`fetch()`采用模块化设计，API 分散在多个对象上（Response 对象、Request 对象、Headers 对象），更合理一些；相比之下，XMLHttpRequest 的 API 设计并不是很好，输入、输出、状态都在同一个接口管理，容易写出非常混乱的代码。

（3）`fetch()`通过数据流（Stream 对象）处理数据，可以分块读取，有利于提高网站性能表现，减少内存占用，对于请求大文件或者网速慢的场景相当有用。XMLHTTPRequest 对象不支持数据流，所有的数据必须放在缓存里，不支持分块读取，必须等待全部拿到后，再一次性吐出来。

此外fetch用法上与ajax的不同在于：

1. fecth返回的promise将不会拒绝http错误状态，即使响应是一个http 404或者500，相反，它会正常解决，并且仅在网络故障时或者任何阻止请求完成时才会拒绝
2. 默认情况下，fetch在服务端不会发送或者接收任何cookie，如果站点依赖于维护一个用户会话，则导致未经认证的请求(请求中未带cookie)。这个也可以自动处理，如果想要在同域中自动发送cookie，加上same-origin就可以

```javascript
fetch(url, {
  credencials: 'include'
})
```



在用法上，`fetch()`接受一个 URL 字符串作为参数，默认向该网址发出 GET 请求，返回一个 Promise 对象。它的基本用法如下。

`fetch()`发出请求以后，有一个很重要的注意点：只有网络错误，或者无法连接时，`fetch()`才会报错，其他情况都不会报错，而是认为请求成功。

这就是说，即使服务器返回的状态码是 4xx 或 5xx，`fetch()`也不会报错（即 Promise 不会变为 `rejected`状态）。

只有通过`Response.status`属性，得到 HTTP 回应的真实状态码，才能判断请求是否成功

```javascript
async function fetchText() {
  let response = await fetch('/readme.txt');
  if (response.status >= 200 && response.status < 300) {
    return await response.text();
  } else {
    throw new Error(response.statusText);
  }
}
```

`fetch()`请求发送以后，如果中途想要取消，需要使用`AbortController`对象。

```javascript
let controller = new AbortController();
let signal = controller.signal;

fetch(url, {
  signal: controller.signal
});

signal.addEventListener('abort',
  () => console.log('abort!')
);

controller.abort(); // 取消

console.log(signal.aborted); // true
```



#### arraybuffer blob filereader

Blob 对象表示一个二进制文件的数据内容，比如一个图片文件的内容就可以通过 Blob 对象读写。它通常用来读写文件，它的名字是 Binary Large Object （二进制大型对象）的缩写。它与 ArrayBuffer 的区别在于，它用于操作二进制文件，而 ArrayBuffer 用于操作内存。

`Blob`构造函数接受两个参数。第一个参数是数组，成员是字符串或二进制对象，表示新生成的`Blob`实例对象的内容；第二个参数是可选的，是一个配置对象，目前只有一个属性`type`，它的值是一个字符串，表示数据的 MIME 类型，默认是空字符串。

文件选择器`<input type="file">`用来让用户选取文件。出于安全考虑，浏览器不允许脚本自行设置这个控件的`value`属性，即文件必须是用户手动选取的，不能是脚本指定的。一旦用户选好了文件，脚本就可以读取这个文件。

文件选择器返回一个 FileList 对象，该对象是一个类似数组的成员，每个成员都是一个 File 实例对象。File 实例对象是一个特殊的 Blob 实例，增加了`name`和`lastModifiedDate`属性。



取得 Blob 对象以后，可以通过`FileReader`对象，读取 Blob 对象的内容，即文件内容。

FileReader 对象提供四个方法，处理 Blob 对象。Blob 对象作为参数传入这些方法，然后以指定的格式返回。

- `FileReader.readAsText()`：返回文本，需要指定文本编码，默认为 UTF-8。
- `FileReader.readAsArrayBuffer()`：返回 ArrayBuffer 对象。
- `FileReader.readAsDataURL()`：返回 Data URL。
- `FileReader.readAsBinaryString()`：返回原始的二进制字符串。

```javascript
function typefile(file) {
  // 文件开头的四个字节，生成一个 Blob 对象
  var slice = file.slice(0, 4);
  var reader = new FileReader();
  // 读取这四个字节
  reader.readAsArrayBuffer(slice);
  reader.onload = function (e) {
    var buffer = reader.result;
    // 将这四个字节的内容，视作一个32位整数
    var view = new DataView(buffer);
    var magic = view.getUint32(0, false);
    // 根据文件的前四个字节，判断它的类型
    switch(magic) {
      case 0x89504E47: file.verified_type = 'image/png'; break;
      case 0x47494638: file.verified_type = 'image/gif'; break;
      case 0x25504446: file.verified_type = 'application/pdf'; break;
      case 0x504b0304: file.verified_type = 'application/zip'; break;
    }
    console.log(file.name, file.verified_type);
  };
}
```

##### promise封装fileReader

创建一个 Promise 对象，等待 FileReader 读取完成后调用 `resolve` 方法，或者出现问题时调用 `reject` 方法。

```javascript
function reader (file, options) {
  options = options || {};
  return new Promise(function (resolve, reject) {
    let reader = new FileReader();

    reader.onload = function () {
      resolve(reader);
    };
    reader.onerror = reject;

    if (options.accept && !new RegExp(options.accept).test(file.type)) {
      reject({
        code: 1,
        msg: 'wrong file type'
      });
    }

    if (!file.type || /^text\//i.test(file.type)) {
      reader.readAsText(file);
    } else {
      reader.readAsDataURL(file);
    }
  });
}
```



#### File

File 对象代表一个文件，用来读写文件信息。

File的属性：

- File.lastModified：最后修改时间
- File.name：文件名或文件路径
- File.size：文件大小（单位字节）
- File.type：文件的 MIME 类型

### FormData

表单（`<form>`）用来收集用户提交的数据，发送到服务器。比如，用户提交用户名和密码，让服务器验证，就要通过表单。表单提供多种控件，让开发者使用

用户点击“提交”按钮，每一个控件都会生成一个键值对，键名是控件的`name`属性，键值是控件的`value`属性，键名和键值之间由等号连接。比如，用户名输入框的`name`属性是`user_name`，`value`属性是用户输入的值，假定是“张三”，提交到服务器的时候，就会生成一个键值对`user_name=张三`。

所有的键值对都会提交到服务器。但是，提交的数据格式跟`<form>`元素的`method`属性有关。该属性指定了提交数据的 HTTP 方法。如果是 GET 方法，所有键值对会以 URL 的查询字符串形式，提交到服务器，比如`/handling-page?user_name=张三&user_passwd=123&submit_button=提交`

如果是 POST 方法，所有键值对会连接成一行，作为 HTTP 请求的数据体发送到服务器，比如`user_name=张三&user_passwd=123&submit_button=提交`。

注意，实际提交的时候，只要键值不是 URL 的合法字符（比如汉字“张三”和“提交”），浏览器会自动对其进行编码。

注意，表单里面的`<button>`元素如果没有用`type`属性指定类型，那么默认就是`submit`控件

除了点击`submit`控件提交表单，还可以用表单元素的`submit()`方法，通过脚本提交表单

表单元素的`reset()`方法可以重置所有控件的值（重置为默认值）。

```javascript
formElement.submit();
formElement.reset();
```

表单数据以键值对的形式向服务器发送，这个过程是浏览器自动完成的。但是有时候，我们希望通过脚本完成这个过程，构造或编辑表单的键值对，然后通过脚本发送给服务器。浏览器原生提供了 FormData 对象来完成这项工作。

`FormData()`首先是一个构造函数，用来生成表单的实例。

```javascript
var formdata = new FormData(form);
```

`FormData()`构造函数的参数是一个 DOM 的表单元素，构造函数会自动处理表单的键值对。这个参数是可选的，如果省略该参数，就表示一个空的表单。

```html
<form id="myForm" name="myForm">
  <div>
    <label for="username">用户名：</label>
    <input type="text" id="username" name="username">
  </div>
  <div>
    <label for="useracc">账号：</label>
    <input type="text" id="useracc" name="useracc">
  </div>
  <div>
    <label for="userfile">上传文件：</label>
    <input type="file" id="userfile" name="userfile">
  </div>
<input type="submit" value="Submit!">
</form>
<script>
var myForm = document.getElementById('myForm');
var formData = new FormData(myForm);

// 获取某个控件的值
formData.get('username') // ""

// 设置某个控件的值
formData.set('username', '张三');

formData.get('username') // "张三"
</script>
```

FormData 提供以下实例方法。

- `FormData.get(key)`：获取指定键名对应的键值，参数为键名。如果有多个同名的键值对，则返回第一个键值对的键值。
- `FormData.getAll(key)`：返回一个数组，表示指定键名对应的所有键值。如果有多个同名的键值对，数组会包含所有的键值。
- `FormData.set(key, value)`：设置指定键名的键值，参数为键名。如果键名不存在，会添加这个键值对，否则会更新指定键名的键值。如果第二个参数是文件，还可以使用第三个参数，表示文件名。
- `FormData.delete(key)`：删除一个键值对，参数为键名。
- `FormData.append(key, value)`：添加一个键值对。如果键名重复，则会生成两个相同键名的键值对。如果第二个参数是文件，还可以使用第三个参数，表示文件名。
- `FormData.has(key)`：返回一个布尔值，表示是否具有该键名的键值对。
- `FormData.keys()`：返回一个遍历器对象，用于`for...of`循环遍历所有的键名。
- `FormData.values()`：返回一个遍历器对象，用于`for...of`循环遍历所有的键值。
- `FormData.entries()`：返回一个遍历器对象，用于`for...of`循环遍历所有的键值对。如果直接用`for...of`循环遍历 FormData 实例，默认就会调用这个方法。

内置验证

表单提交的时候，浏览器允许开发者指定一些条件，它会自动验证各个表单控件的值是否符合条件。

```html
<!-- 必填 -->
<input required>

<!-- 必须符合正则表达式 -->
<input pattern="banana|cherry">

<!-- 字符串长度必须为6个字符 -->
<input minlength="6" maxlength="6">

<!-- 数值必须在1到10之间 -->
<input type="number" min="1" max="10">

<!-- 必须填入 Email 地址 -->
<input type="email">

<!-- 必须填入 URL -->
<input type="URL">
```

如果一个控件通过验证，它就会匹配`:valid`的 CSS 伪类，浏览器会继续进行表单提交的流程。如果没有通过验证，该控件就会匹配`:invalid`的 CSS 伪类，浏览器会终止表单提交，并显示一个错误信息s

```css
input:invalid {
  border-color: red;
}
input,
input:valid {
  border-color: #ccc;
}
```

除了提交表单的时候，浏览器自动校验表单，还可以手动触发表单的校验。表单元素和表单控件都有`checkValidity()`方法，用于手动触发校验

`checkValidity()`方法返回一个布尔值，`true`表示通过校验，`false`表示没有通过校验。

```javascript
// 触发整个表单的校验
form.checkValidity()

// 触发单个表单控件的校验
formControl.checkValidity()
```

控件元素的`validationMessage`属性返回一个字符串，表示控件不满足校验条件时，浏览器显示的提示文本。以下两种情况，该属性返回空字符串。

- 该控件不会在提交时自动校验
- 该控件满足校验条件

控件元素的`setCustomValidity()`方法用来定制校验失败时的报错信息。它接受一个字符串作为参数，该字符串就是定制的报错信息。如果参数为空字符串，则上次设置的报错信息被清除。

这个方法可以替换浏览器内置的表单验证报错信息，参数就是要显示的报错信息

控件元素的属性`validity`属性返回一个`ValidityState`对象，包含当前校验状态的信息。

该对象有以下属性，全部为只读属性。

- `ValidityState.badInput`：布尔值，表示浏览器是否不能将用户的输入转换成正确的类型，比如用户在数值框里面输入字符串。
- `ValidityState.customError`：布尔值，表示是否已经调用`setCustomValidity()`方法，将校验信息设置为一个非空字符串。
- `ValidityState.patternMismatch`：布尔值，表示用户输入的值是否不满足模式的要求。
- `ValidityState.rangeOverflow`：布尔值，表示用户输入的值是否大于最大范围。
- `ValidityState.rangeUnderflow`：布尔值，表示用户输入的值是否小于最小范围。
- `ValidityState.stepMismatch`：布尔值，表示用户输入的值不符合步长的设置（即不能被步长值整除）。
- `ValidityState.tooLong`：布尔值，表示用户输入的字数超出了最长字数。
- `ValidityState.tooShort`：布尔值，表示用户输入的字符少于最短字数。
- `ValidityState.typeMismatch`：布尔值，表示用户填入的值不符合类型要求（主要是类型为 Email 或 URL 的情况）。
- `ValidityState.valid`：布尔值，表示用户是否满足所有校验条件。
- `ValidityState.valueMissing`：布尔值，表示用户没有填入必填的值。

文件上传

用户上传文件，也是通过表单。具体来说，就是通过文件输入框选择本地文件，提交表单的时候，浏览器就会把这个文件发送到服务器。

```html
<input type="file" id="file" name="myFile">
```

此外，还需要将表单`<form>`元素的`method`属性设为`POST`，`enctype`属性设为`multipart/form-data`。其中，`enctype`属性决定了 HTTP 头信息的`Content-Type`字段的值，默认情况下这个字段的值是`application/x-www-form-urlencoded`，但是文件上传的时候要改成`multipart/form-data`

```html
<form method="post" enctype="multipart/form-data">
  <div>
    <label for="file">选择一个文件</label>
    <input type="file" id="file" name="myFile" multiple>
  </div>
  <div>
    <input type="submit" id="submit" name="submit_button" value="上传" />
  </div>
</form>
```

然后在JavaScript中获取文件

```javascript
var fileSelect = document.getElementById('file');
var files = fileSelect.files;

var formData = new FormData();

for (var i = 0; i < files.length; i++) {
  var file = files[i];

  // 只上传图片文件
  if (!file.type.match('image.*')) {
    continue;
  }

  formData.append('photos[]', file, file.name);
}

var xhr = new XMLHttpRequest();

xhr.open('POST', 'handler.php', true);

xhr.onload = function () {
  if (xhr.status !== 200) {
    console.log('An error occurred!');
  }
};

xhr.send(formData);
```

除了发送 FormData 实例，也可以直接 AJAX 发送文件。

```javascript
var file = document.getElementById('test-input').files[0];
var xhr = new XMLHttpRequest();

xhr.open('POST', 'myserver/uploads');
xhr.setRequestHeader('Content-Type', file.type);
xhr.send(file);
```



### WebWorker

JavaScript 语言采用的是单线程模型，也就是说，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程带来很大的不便，无法充分发挥计算机的计算能力。

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务可以交由 Worker 线程执行，主线程（通常负责 UI 交互）能够保持流畅，不会被阻塞或拖慢。

Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动（比如用户点击按钮、提交表单）打断。这样有利于随时响应主线程的通信。但是，这也造成了 Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，就应该关闭。

Web Worker 有以下几个使用注意点。

（1）**同源限制**

分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。

（2）**DOM 限制**

Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用`document`、`window`、`parent`这些对象。但是，Worker 线程可以使用`navigator`对象和`location`对象。

（3）**全局对象限制**

Worker 的全局对象`WorkerGlobalScope`，不同于网页的全局对象`Window`，很多接口拿不到。比如，理论上 Worker 线程不能使用`console.log`，因为标准里面没有提到 Worker 的全局对象存在`console`接口，只定义了`Navigator`接口和`Location`接口。不过，浏览器实际上支持 Worker 线程使用`console.log`，保险的做法还是不使用这个方法。

（4）**通信联系**

Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。

（5）**脚本限制**

Worker 线程不能执行`alert()`方法和`confirm()`方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。

（6）**文件限制**

Worker 线程无法读取本地文件，即不能打开本机的文件系统（`file://`），它所加载的脚本，必须来自网络。

主线程采用`new`命令，调用`Worker()`构造函数，新建一个 Worker 线程。

`Worker()`构造函数的参数是一个脚本文件，该文件就是 Worker 线程所要执行的任务。由于 Worker 不能读取本地文件，所以这个脚本必须来自网络。如果下载没有成功（比如404错误），Worker 就会默默地失败。

使用完毕，为了节省系统资源，必须关闭 Worker。

```javascript
// 主线程
worker.terminate();

// Worker 线程
self.close();
```

主线程与 Worker 之间的通信内容，可以是文本，也可以是对象。需要注意的是，这种通信是拷贝关系，即是传值而不是传址，Worker 对通信内容的修改，不会影响到主线程。事实上，浏览器内部的运行机制是，先将通信内容串行化，然后把串行化后的字符串发给 Worker，后者再将它还原。

主线程与 Worker 之间也可以交换二进制数据，比如 File、Blob、ArrayBuffer 等类型，也可以在线程之间发送。

```javascript
// 主线程
var uInt8Array = new Uint8Array(new ArrayBuffer(10));
for (var i = 0; i < uInt8Array.length; ++i) {
  uInt8Array[i] = i * 2; // [0, 2, 4, 6, 8,...]
}
worker.postMessage(uInt8Array);

// Worker 线程
self.onmessage = function (e) {
  var uInt8Array = e.data;
  postMessage('Inside worker.js: uInt8Array.toString() = ' + uInt8Array.toString());
  postMessage('Inside worker.js: uInt8Array.byteLength = ' + uInt8Array.byteLength);
};
```

`Worker()`构造函数返回一个 Worker 线程对象，用来供主线程操作 Worker。Worker 线程对象的属性和方法如下。

- Worker.onerror：指定 error 事件的监听函数。
- Worker.onmessage：指定 message 事件的监听函数，发送过来的数据在`Event.data`属性中。
- Worker.onmessageerror：指定 messageerror 事件的监听函数。发送的数据无法序列化成字符串时，会触发这个事件。
- Worker.postMessage()：向 Worker 线程发送消息。
- Worker.terminate()：立即终止 Worker 线程。

 [Broadcast Channel](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FBroadcastChannel) 是为了通信更常用的API。它可以让我们广播信息到同源的所有上下文。同源的所有的浏览器tab,iframes或者worker 都可以收发信息

```javascript
// Connection to a broadcast channel
var bc = new BroadcastChannel('test_channel');

// Example of sending of a simple message
bc.postMessage('This is a test message.');

// Example of a simple event handler that only
// logs the message to the console
bc.onmessage = function (e) { 
  console.log(e.data); 
}

// Disconnect the channel
bc.close()
```

Web Worker 有自己的全局对象，不是主线程的`window`，而是一个专门为 Worker 定制的全局对象。因此定义在`window`上面的对象和方法不是全部都可以使用。

Worker 线程有一些自己的全局属性和方法。

- self.name： Worker 的名字。该属性只读，由构造函数指定。
- self.onmessage：指定`message`事件的监听函数。
- self.onmessageerror：指定 messageerror 事件的监听函数。发送的数据无法序列化成字符串时，会触发这个事件。
- self.close()：关闭 Worker 线程。
- self.postMessage()：向产生这个 Worker 的线程发送消息。
- self.importScripts()：加载 JS 脚本。

#### sharedWorker

shared web worker：运行的是更为普遍性的代码，可以为多个页面服务。它可以被与之相关联的多个页面访问，只有当所有关联的的页面都关闭的时候，该Shared web worker才会结束

创建shared web worker与创建dedicated web worker方法类似，调用Shared`Worker()`构造函数，指定一个要在 worker 线程内运行的脚本的 uri。

```javascript
var myWorker = new SharedWorker("worker.js");
```

与dedicated web worker不同的是，shared web worker访问worker通过sharedworker.port属性创建了一个messageport对象，该对象可以用来进行通信和对共享进程进行控制。当使用addEventListener监听message事件时，端口需要手动启动，利用其start()方法，采用onmessage()则不用

端口开启后，使用port.postmessage()向SharedWorker发送消息，使用port.onmessage监听事件接收SharedWorker传递的消息

#### 使用webworker的场景

**光线追踪**: 光线追踪是一种渲染技术，将跟踪的光线路径作为纹理生成图像。光线追踪是一个非常重的CPU密集型计算，需要模拟一些效果，像反射，折射，材质等等。所有这些计算逻辑需要被添加到Worker，以避免UI线程的阻塞。更棒的是，你可以在几个worker之间很随意的切割图像渲染（可以利用多个CPU）。这里是一个简单的例子 — [nerget.com/rayjs-mt/ra…](https://link.juejin.cn?target=https%3A%2F%2Fnerget.com%2Frayjs-mt%2Frayjs.html).

**加密:** 端到端的加密越来越流行了，因为个人信息和敏感信息的要求越来越高。加密是相当耗时的，尤其是如果加密的数据量很大（比如，发往服务器前加密数据）。这是使用worker的绝佳场景，因为它不需要访问DOM。一旦进入worker，它对用户是无感的，也不会影响用户体验。

**预加载数据:** 为了优化你的代码，提升加载事件，你可以使用worker去提前加载和存储数据，这样你可以在需要的时候使用它们。这种场景下，Worker的表现是非常棒的。

**渐进式网络应用:**  即使网络不稳定，这些应用也可以很快加载。也就是说数据需要被存储在浏览器本地。这里就需要[IndexDB](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FAPI%2FIndexedDB_API)和一些类似的API了。这都是客户端存储需要的。为了不阻塞UI，这些东西都要放在Worker中执行。当使用 IndexDB的时候，可以不使用 workers 而使用其异步接口，但是之前它也含有同步接口（可能会再次引入 ），这时候就必须在 workers 中使用 IndexDB。

**拼写检查:** 一个常见的拼写检查是这么工作的---程序读取带着正确拼写的字典文件。这个字典被转换长一个搜查树好让文本搜索更有效，当cheker提供了一个单词，程序会检查它是否在树中。如果不在，则会通过提供替代的字符为用户提供替代的拼写，并检查它是否是用户想写的。这个过程可以轻松的转给Worker来做，这样当用户输入任何单词或者句子，都不会阻塞UI，同时执行所有的搜索和拼写建议。

#### 使用举例

轮询

```javascript
function createWorker(f) {
  var blob = new Blob(['(' + f.toString() +')()']);
  var url = window.URL.createObjectURL(blob);
  var worker = new Worker(url);
  return worker;
}

var pollingWorker = createWorker(function (e) {
  var cache;

  function compare(new, old) { ... };

  setInterval(function () {
    fetch('/my-api-endpoint').then(function (res) {
      var data = res.json();

      if (!compare(data, cache)) {
        cache = data;
        self.postMessage(data);
      }
    })
  }, 1000)
});

pollingWorker.onmessage = function () {
  // render data
}

pollingWorker.postMessage('init');
```



### URL

URL API 是一个 URL 标准的组件，它定义了有效的[Uniform Resource Locator](https://developer.mozilla.org/zh-CN/docs/Glossary/URL)和访问、操作 URL 的 API。URL 标准还定义了像域名、主机和 IP 地址等概念，并尝试以标准的方式去描述用于以键/值对的形式提交

URL 标准的主要内容是由 URL 的定义以及它的结构和解析方式决定的。还介绍了与网络上计算机寻址有关的各种术语的定义，并指定了解析 IP 地址和 DOM 地址的算法。

给指定的 URL 创建一个 [`URL`](https://developer.mozilla.org/zh-CN/docs/Web/API/URL) 对象将解析 URL 并通过其属性对其组成部分的快速访问。

```javascript
let addr = new URL("https://developer.mozilla.org/en-US/docs/Web/API/URL_API");
let host = addr.host;
let path = addr.pathname;
```

**`URLSearchParams`** 接口定义了一些实用的方法来处理 URL 的查询字符串

一个实现了 `URLSearchParams` 的对象可以直接用在 [`for...of`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of) 结构中

[`URLSearchParams.append()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/append)：插入一个指定的键/值对作为新的搜索参数。

[`URLSearchParams.delete()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/delete)：从搜索参数列表里删除指定的搜索参数及其对应的值。

[`URLSearchParams.entries()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/entries)：返回一个[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)可以遍历所有键/值对的对象。

[`URLSearchParams.get()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/get)：获取指定搜索参数的第一个值。

[`URLSearchParams.getAll()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/getAll)：获取指定搜索参数的所有值，返回是一个数组。

[`URLSearchParams.has()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/has)：返回 [`Boolean`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Boolean) 判断是否存在此搜索参数。

[`URLSearchParams.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/keys)：返回[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols) 此对象包含了键/值对的所有键名。

[`URLSearchParams.set()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/set)：设置一个搜索参数的新值，假如原来有多个值将删除其他所有的值。

[`URLSearchParams.sort()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/sort)：按键名排序。

[`URLSearchParams.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/toString)：返回搜索参数组成的字符串，可直接使用在 URL 上。

[`URLSearchParams.values()`](https://developer.mozilla.org/zh-CN/docs/Web/API/URLSearchParams/values)：返回[`iterator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols) 此对象包含了键/值对的所有值。



### 新型Web api

#### IntersectionObserver

传统检查元素可见性的方法是通过监听scroll事件，调用目标元素的getBoundingClientRect()方法，得到它相对视口左上角的坐标，再判断是否在视口之内。这种方法的缺点是由于scroll事件密集发生，计算量很大，容易造成性能问题。

Intersection Observer API就是解决这个问题，可以自动观察判断元素是否可见。由于可见的本质是目标元素与视口产生了一个交叉区，因此这个API叫做交叉观察器

利用这个API可以轻松实现无限滚动列表、图片懒加载、视频自动播放等功能

图片懒加载

```javascript

```

#### Page visibility



#### Geolocation API



#### Web share
