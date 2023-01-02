---
title: HTML/CSS
date: 2020-08-02 21:40:33
categories: 技术博客
tags:
    - Web，IT，HTML，CSS
toc: true
thumbnail: https://cdn.kunkunzhang.top/stylecomponent.png
---

​      css in js以及css的一些动画

<!--more-->

## CSS

### CSS动画

css中动画的原理是从一套css样式变到另一套css样式

变形（tansform）

属性

rotate:以中心为原点旋转一定角度，分为rotate()、rotateX()、rotateY()、rotateZ()、rotate3D()，rotate(10deg)表示倾斜10度

skew:倾斜，分为skew(x,y),skewX(x),skewY(x)

Scale:缩放，分为scale(x,y),scaleX(x),scaleY(y)、scaleZ(z)、scale3D(z)。scale(1.5)表示1.5的比例放大，若为负表示缩小

translate:位移，分为translateX(),translateY(),translateZ(),translate3D()

Matrix:变换综合应用，矩阵变换



过渡动画transition

类似于hero动画，在同一元素的两种样式之间添加过渡动画

`transition-property`：指定哪个或哪些 CSS 属性用于过渡。只有指定的属性才会在过渡中发生动画，其它属性仍如通常那样瞬间变化。

`transition-duration`指定过渡的时长。或者为所有属性指定一个值，或者指定多个值，为每个属性指定不同的时长。

`transition-timing-function`指定一个函数，定义属性值怎么变化。

`transition-delay`指定延迟，即属性开始变化时与过渡开始发生时之间的时长。



动画

Animation

animation是css3新添加的属性，用来为元素实现动画效果。animation无法单独承担动画的效果，需要承载动画的另一个属性-@keyframes，@keyframes定义的动画并不直接执行，需要借助animation来运转

通过百分比来规定动画中发生改变的时间。0%表示动画开始的时间，50%表示动画执行到一半的时间，100%表示动画结束的时间。百分比后的花括号动画在该节点要达到的效果。也可以用关键字表示节点，from表示0%，to表示100%

实例

```css
@keyframes animationname{
  keyframes-selector {
    css-style
  }
}
/*定义mymove动画，定义0%、25%、50%、75%、100%五个阶段的样式*/
@keyframes mymove {
  0%{top:0px; background:red; width:100px; -webkit-transform: rotate(0deg);
        transform: rotate(0deg);}
  25%{top:200px;}
  50%{top:100px;}
  75%{top:200px;}
  100%{top:200px; background:yellow; width:300px;
    -webkit-transform:rotate(360deg);transform: rotate(360deg);}
}
/*在load-border中引入mymove animation，定义线性与非线性、循环等属性*/
.load-border {
    width: 120px;
    height: 120px;
    background: url(../images/loading_icon.png) no-repeat center center;
    -webkit-animation: mymove 1.4s infinite linear;
    animation: mymove 1.4s infinite linear; 
}
```

使用时为了兼容不同浏览器，可以加上-webkit-、-o-、-ms-、-moz-、-khtml-等前缀

#### transform与transition、translate区别

transform用于移动dom，translate是transition的一个属性，表示移动dom， transition用于在两个样式之间添加过渡动画

例：

```css
div {
  transform: rotate(90deg);
  transition: transform 0.3s ease;
}
```



#### js实现css动画

纯css animation或者transform的动画，js不好进行控制，因此可以使用js生成基于element.animate的动画，以便使用web animate api和其他事件(如点击按钮)进行互动。

```javascript
//像css keyframes一样，先定义关键帧
var boxframes = [
  {
    transform:'tranlateX(0)',
    background:'red',
    borderRadius:0
  },
  {
    transform:'translateX(45vw) scale(.5)',
    background:'orange',
    borderRadius:0
  },
  {
    transform:'translateX(90vw)',
    background:'green',
    borderRadius:'50%'
  }
]
//获取要执行动画的DOM
var boxref = document.getElementById("box")
//在该DOM上通过element.animate api执行动画
var boxanimation = boxref.animate(boxframes,
 {
  duration: 5000,//动画执行时长
  fill:'both',
  easing:'linear',
  iterations:'Infinity',
})
```

其他对动画的操作通过动画对象实现

实例对象的属性：

```javascript
boxanimation.currentTime:获取或设置动画的当前时间值
boxanimation.effect：获取或设置动画的目标效果
boxanimation.id 用于表示动画的字符串
boxanimation.playbackRate:用于获取或设置动画回放速率的整数，1表示支持，0表示暂停，2表示双倍，-1表示反向
boxanimation.ready：
boxanimation.finish：
boxanimation.startTime：
boxanimation.timeline：
```

实例对象的方法：

```javascript
boxanimation.cancel() //取消动画
boxanimation.finish()  //立即完成一个动画
boxanimation.pause()  //暂停动画
boxanimation.play()   //播放动画
boxanimation.reverse()  //颠倒动画的方向，反向播放
```

实例对象的事件

```javascript
//当动画取消时触发
boxanimation.cancle = function(){
  boxanimation.reverse();
}
//当动画完成时触发
boxanimation.finish = function(){
  boxanimation.reverse();
}
```

#### 常用动画

##### 上浮/下浮

上浮

```css
ul li {
	transform-style: preserve-3d;
	-webkit-transform-style: preserve-3d;
	-ms-transform-style: preserve-3d;
	-moz-transform-style: preserve-3d;
	transition: 0.5s;
	-webkit-transition: 0.5s;
	-ms-transition: 0.5s;
	-moz-transition: 0.5s;
}
ul li:hover {
	transform: translateZ(30px) scale(1.1);
	-webkit-transform: translateZ(30px) scale(1.1);
	-ms-transform: translateZ(30px) scale(1.1);
	-moz-transform: translateZ(30px) scale(1.1);
}
```

下浮

```css
ul li {
	transform-style: preserve-3d;
	-webkit-transform-style: preserve-3d;
	-ms-transform-style: preserve-3d;
	-moz-transform-style: preserve-3d;
	transition: 0.5s;
	-webkit-transition: 0.5s;
	-ms-transition: 0.5s;
	-moz-transition: 0.5s;
}
 
ul li:hover {
	transform: translateZ(30px) scale(0.9);
	-webkit-transform: translateZ(30px) scale(0.9);
	-ms-transform: translateZ(30px) scale(0.9);
	-moz-transform: translateZ(30px) scale(0.9);
}
```

##### 前后翻转

```css
#nav1 ul li {
	transform-style: preserve-3d;
	-webkit-transform-style: preserve-3d;
	-ms-transform-style: preserve-3d;
	-moz-transform-style: preserve-3d;
	transition: 0.5s;
	-webkit-transition: 0.5s;
	-ms-transition: 0.5s;
	-moz-transition: 0.5s;
}
 
#nav1 ul li:hover {
	transform: translateZ(30px) rotateX(360deg) scale(1.1);
	-webkit-transform: translateZ(30px) rotateX(360deg) scale(1.1);
	-ms-transform: translateZ(30px) rotateX(360deg) scale(1.1);
	-moz-transform: translateZ(30px) rotateX(360deg) scale(1.1);
}
```

##### 左右翻转

```css
ul li {
  transform-style: perserve-3d;
  -webkit-transform-style: preserve-3d;
	-ms-transform-style: preserve-3d;
	-moz-transform-style: preserve-3d;
  transition: 0.5s;
  -webkit-transition: 0.5s;
	-ms-transition: 0.5s;
	-moz-transition: 0.5s;
}
ul li:hover {
  transform: translateZ(30px) rotateY(360deg) scale(1.1);
  -webkit-transform: translateZ(30px) rotateY(360deg) scale(1.1);
	-ms-transform: translateZ(30px) rotateY(360deg) scale(1.1);
	-moz-transform: translateZ(30px) rotateY(360deg) scale(1.1);
}
```

##### 向下展开/收起

向下展开一般是改变height，但是如果同时有移动端和pc端两种样式，height一般设置为auto，但是transition只支持具体值的heigth，不支持height:auto，也就是height设置auto时不触发transition

技巧：一直设置height为auto，然后通过设置max-height设置动态高度

```css
{
  max-height: 0;
  height: auto
  transition: max-height 0.3s linear;
  
  &.up {
    max-height: 0;
  }
  &.down {
    max-height: 1000px;
  }
}
```

##### 伪元素实现边框颜色变化



### js/jquery动画与css动画的区别

这个涉及到浏览器实现原理

渲染引擎生成一帧图像时，有三种方式：重排、重绘、合成。其中重排和重绘都是在渲染进程的主线程上完成的，比较耗时，而合成操作是在渲染进程的合成线程上执行的，执行速度快，且不占用主线程

jquery动画较慢的原因

- jQuery 不能避免 [layout thrashing](http://wilsonpage.co.uk/preventing-layout-thrashing/) （有人喜欢将其翻译为“布局颠簸”，会导致多余relayout/reflow），因为它的代码不仅仅用于动画，它还用于很多其他场景。
- jQuery的内存消耗较大，经常会触发垃圾回收。而垃圾回收触发时很容易[让动画卡住](http://blog.artillery.com/2012/10/browser-garbage-collection-and-framerate.html)。

css动画

CSS transition 的动画逻辑是由浏览器来执行，所以它的性能能够比 jQuery 动画好。它的优势体现在：

1. 通过优化 DOM 操作，避免内存消耗来减少卡顿
2. 使用与 RAF 类似的机制
3. 强制使用硬件加速 （通过 GPU 来提高动画性能）

js动画可以实现和css动画相似的性能



### 监测动画掉帧的方法

Chrome dev tool中Timeline的Frame模块

地址栏输入“chrome:flags”搜索“fps”，将FPS计数器开启，浏览器重启后右上角会实时显示帧速率



### 滚动视差的实现

`background-attachment`：如果指定了 `background-image` ，那么 `background-attachment` 决定背景是在视口中固定的还是随着包含它的区块滚动的。

属性：

`scroll`：表示背景相对于元素本身固定， 而不是随着它的内容滚动。

`local`：表示背景相对于元素的内容固定。如果一个元素拥有滚动机制，背景将会随着元素的内容滚动， 并且背景的绘制区域和定位区域是相对于可滚动的区域而不是包含他们的边框。

`Fixed`:





### 浏览器兼容性问题

使用css时加上-webkit-、-o-、-ms-、-moz-、-khtml-等前缀

### GPU加速

现代的浏览器通常会有两个重要的执行线程，这两个线程协同工作来渲染一个网页：主线程和合成线程

一般情况下，主线程负责运行JavaScript，计算HTML元素的CSS样式，页面的布局，将元素绘制到一个或者多个位图，将这些位图交给合成线程

合成线程负责通过GPU将位图绘制到屏幕上，通知主线程更新页面中可见或即将变成可见的部分的位图，计算出页面中哪部分时可见的，计算出当你滚动页面时哪部分是即将变成可见的，当你滚动页面时即将相应的页面移动到可视区域

CSS animation、transforms以及transitions不会自动开启GPU加速，而是由浏览器缓慢的软件渲染引擎来执行。但是像Chrome、Firefox、Safari和最新的Opera都支持硬件加速。我们需要手动触发。

浏览器会在元素的3D变换时开启，对于没有3D变换的效果，可以使用translateZ(0)来骗过浏览器，开启硬件加速

```css
.cube{
  -webkit-transform:translateZ(0);
  -moz-transform:translateZ(0);
  -ms-transform:translateZ(0);
  -o-transform:translateZ(0);
  transform:translateZ(0);
}
```

在chrome和safari中使用translateZ开启GPU加速后可能会有页面闪烁的问题，使用下面的代码修复此情况

```javascript
.cube{
  -webkit-backface-visibility:hidden;  
  -moz-backface-visibility:hidden;
  -ms-backface-visibility:hidden;
  backface-visibility:hidden;
  
  -webkit-perspective:1000;
  -moz-perspective:1000;
  -ms-perspective:1000;
  perspective:1000;
}
```

在webkit的浏览器中使用另一个行之有效的方法

```css
.cube{
  -webkit-transform:translate3d(0,0,0);  
  -moz-transform:translate3d(0,0,0);
  -ms-transform:translate3d(0,0,0);
  transform:translate3d(0,0,0);
}
```

原生的移动端应用总是可以很好地应用GPU，这就是它比网页应用表现更好的原因，硬件加速在移动端尤其有用，因为它可以有效地减少资源地利用

适合使用transform3d或者translateZ开启GPU硬件加速的使用范围：

- 使用很多大尺寸图片(尤其是PNG24)进行动画的时候

- 页面有很多大尺寸图片并且进行了CSS缩放处理，页面可以滚动时

- 使用background-size：cover设置大尺寸背景图，并且页面可以滚动时

- 编写大量DOM元素进行CSS动画时(transition/transform/keyframes/absTop/left)

- 使用很多PNG图片拼接成CSS Sprite时

GPU缺点：

GPU增加了内存的使用，而且它会减少移动端电池的使用寿命，所以确保需要时使用

### SCSS

**[Sass](https://link.zhihu.com/?target=http%3A//sass-lang.com/)**是成熟、稳定、强大的**CSS预处理器**，**SCSS**是**Sass3**版本当中引入的新语法特性，完全兼容CSS3的同时继承了**Sass**强大的动态功能。

[Scss](https://sass-lang.com/) 是 CSS 的扩展， 在保证兼容性的基础上， 允许使用变量、 嵌套、 混合、 导入等特性， 在编写大量的 CSS 文件时很有帮助。

Scss 是 CSS3 的扩展， 在 CSS3 的基础上， 添加了下面几个重要的特性。

支持使用变量

Scss 支持使用 `$` 符号来定义变量， 支持的变量类型有 `数字（可带单位）`、 `字符串` 、`颜色` 以及 `布尔值`、数组list、对象map、函数function 等，

css支持两种类型的字符串，使用单/双引号的和没有使用引号的。css可以自动识别两种字符串。

```scss
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

loader生成的css文件为

```css
body {
  font: 100% Helvetica, sans-serif;
  color: #333;
}
```

更为直观的嵌套语法

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

输出的css文件为

```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

nav li {
  display: inline-block;
}

nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

支持导入其他文件

```scss
// _reset.Scss
html,
body,
ul,
ol {
   margin: 0;
  padding: 0;
}

/* base.Scss */
@import 'reset';

body {
  font-size: 100% Helvetica, sans-serif;
  background-color: #efefef;
}
```

输出的css格式为

```css
html, body, ul, ol {
  margin: 0;
  padding: 0;
}

body {
  background-color: #efefef;
  font-size: 100% Helvetica, sans-serif;
}
```

继承

```scss
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}

.error {
  @extend .message;
  border-color: red;
}

.warning {
  @extend .message;
  border-color: yellow;
}
```

输出的css文件为

```css
.message, .success, .error, .warning {
  border: 1px solid #cccccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: yellow;
}
```

分部

以下划线开头的文件 (`_partial.Scss`) 不会被输出， 可以被导入到其它文件。

支持运算符

scss支持加减乘除等基本运算符

```scss
.container { width: 100%; }

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;
}

aside[role="complimentary"] {
  float: right;
  width: 300px / 960px * 100%;
}
```

输出css文件为

```css
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}

aside[role="complimentary"] {
  float: right;
  width: 31.25%;
}
```

#### scss与sass、less的区别

Sass(Syntactically Awesome Stylesheet)是一种动态样式语言，比css多出很多功能(如变量、嵌套、运算、混入、继承、颜色处理、函数等等)。

但是sass属于缩排语法，对于习惯CSS前端写法的来说很不直观，因此sass的语法进行了改良，sass 3就变成了scss。scss是css语法的拓展，用{}取代了缩进

Less也是一种动态样式语言，对Css赋予了动态语言的特性，比如变量、继承、运算、函数，less既可以在客户端上运行，也可以在服务端运行

scss与less的区别

1.编译环境不一样

scss是在服务端处理的，以前是Ruby，现在是Dart-Scss或者Node-Scss，而less是需要引入less.js来处理less代码输出css到浏览器，也可以在开发服务器将less语法编译成css再输出css到生产包目录，也有在线编译工具

2.变量符不一样

less是@，scss是$

3.输出设置。Less没有输出设置，Scss提供四种输出设置

nested：嵌套缩进的css代码

Expanded：展开的多行代码

compact:简洁格式的css代码

compressed：压缩后的css代码

4.Scss支持条件语句if{}else{}循环语句for{}循环等等，而less不支持

5.引用外部css文件

scss使用@import引用外部文件，如果不想编译生成同名的css文件，命名必须以_开头，scss会认为该文件是一个引用文件，不会将其编译为同名的css文件

less的引用与css的@import没什么差异

6.scss和less的工具库不同

Less有UI组件库Bootstrap，Bootstrap的样式文件部分源码就是less写的

Scss有工具库Compass，Compass与Scss的关系就像JQuery与JavaScript，封装了一系列有用的模块和模版，补充强化了Scss的功能

7.安装体验不同

使用npm或者yarn安装less很容易，而scss没有翻墙的话容易安装失败

总结：

less和scss都是css的强化版本，scss比less强大，是一种真正的编程语言，而less相对清晰明了，易于上手，对编译环境要求宽松。编译scss一般要安装ruby，ruby官网国内访问不了，所以更倾向于选择less

### Less

继承

```less
.parentClass{
	color:red;
}
.subClassOne{
	&:extend(.parentClass);
}
.subClassTwo:extend(.parentClass){
	
}
```

mixin

```less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}

#menu a {
  color: #111;
  .bordered();
}

.post a {
  color: red;
  .bordered();
}
```





#### less转化成css的过程（lessloader、cssloader）

less配置

```javascript
{
  module: {
    rules: [
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          'less-loader',
        ],
      },
    ],
  },
}
```

涉及到3个loader：less-loader、css-loader、style-loader

Less-loader的作用就是将less代码转译为浏览器可以识别的CSS代码。

Css-loader的作用主要是解析css文件中的@import和url语句，处理css-modules，并将结果作为一个js模块返回。

经过css-loader的转译，我们已经得到了完整的css样式代码，style-loader的作用就是将结果以style标签的方式插入DOM树中。

但css-loader返回的不是css样式代码的文本，而是一个js模块的代码，将这些js代码直接放进style标里显然是不行的。

style-loader的设计思路：

- css-loader返回的样式只能通过其js模块的运行时得到，故使用`require`语句取得
- normal方法实际上什么都没做，在pitch方法里`中断loader链的执行`，再以inline方式调用了后方的loader来加载当前的less文件
- 如果将pitch中的实现放到normal方法里，就会造成loader链执行两遍
- 如果requestPath中没有'!!'前缀，就会造成loader链被无限循环调用

style-loader的实现逻辑比较绕，也是一个比较经典的`pitch`应用，理解了它的原理，就可以是说对loader的调用链、执行顺序和模块化输出等有了一个比较全面的认识。

## bulma

bulma是纯css，没有js，bootstrap有JS。当用vuejs，reactjs时，带有js的css框架并不适合，需要纯的css框架。在好几个项目中用了vue + bulma/buefy，感觉不错

Bulma 是一个手机优先的框架，提供五个宽度断点，分别是 mobile（手机）、tablet（平板）、desktop（桌面）、widescreen（宽屏）、fullHD（高清），具有良好的自适应特性，可以随心所欲为不同设备设置不同样式。

```javascript
mobile：小于等于768pxtablet：大于等于769pxdesktop：大于等于1024pxwidescreen：大于等于1216pxfullhd：大于等于1408px
```

class样式

网格

偏移用`is-offset-`修饰类

- is-offset-one-quarter
- is-offset-one-fifth
- is-offset-8
- is-offset-1

`columns`布局默认是在手机上垂直堆叠，其他宽度都是平铺。如果希望手机也保持平铺，可以加上`is-mobile`修饰类。

如果希望手机和平板是垂直堆叠，其他宽度平铺，可以使用`is-desktop`修饰类。

如果希望在不同设备，网格占据不同的宽度，可以像下面这样写。

```html
// 网格在手机占据二分之一宽度，平板三分之一宽度，桌面四分之一宽度，宽屏和高清则是平铺。<div class="  column  is-half-mobile  is-one-third-tablet  is-one-quarter-desktop"></div>
```

隐藏某个项目

- is-hidden-mobile：只在手机隐藏
- is-hidden-tablet-only：只在平板隐藏
- is-hidden-desktop-only ：只在桌面隐藏
- is-hidden-touch：手机和平板隐藏，其他宽度显示

字体

Bulma 提供7个修饰指定文字大小

```javascript
//设置不同字体is-size-1： 3remis-size-2： 2.5remis-size-3： 2remis-size-4： 1.5remis-size-5： 1.25remis-size-6： 1remis-size-7： 0.75rem//为不同设备指定字体is-size-1-mobile：手机是 size-1is-size-1-tablet：平板是 size-1is-size-1-touch：手机和平板是 size-1is-size-1-desktop：桌面、宽屏和高清是 size-1is-size-1-widescreen：宽屏和高清是 size-1is-size-1-fullhd：高清是 size-1
```



定制

克隆仓库

```shell
git clone https://github.com/jgthms/bulma.git
cd bulma
npm run build
```



 
