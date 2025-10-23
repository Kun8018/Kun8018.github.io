---
title: Javascript开发（十七）
date: 2021-01-18 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/javascript.png
---

​     第四篇主要讲常用的js库.主要偏向于JavaScript的库和前端库，Nodejs的库在第十篇

<!--more-->

## jquery

jquery是目前使用最广泛的js函数库，在使用JavaScript的网站中，93%使用了jquery，成为了开发者必学的技能

jquery的优势有两个，首先它基本是一个DOM操作工具，可以使操作DOM对象变得异常简单，其次它统一 了不同浏览器的api接口，使得代码能够在所有现代浏览器运行，开发者不用担心浏览器之间的差异了

加载jquery可以使用CDN或者本地获取

```html
<script type = "text/javascript"
        src= "//code.jquery.com/jquery-1.11.0.min.js">
</script>
<script>
   window.jQuery || 
   document.write(
     '<script src="js/jquery-1.11.0.min.js" type="text/javascript"></script>'
   );
</script>
```

jquery的基本思想是“先选中某些网页元素，在对其进行某种处理(find something,do something)”

### 与DOM属性相关的方法

addClass、removeClass、toggleClass

addClass用于添加一个类，removeClass用于移除一个类，toggleClass用于折叠一个类，也就是无就添加，有就移除

```javascript
$('li').addClass('special')
$('li').removeClass('special')
$('li').toggleClass('special')
```

css方法

css方法用于获取或者改变css的属性

```javascript
$("h1").css('fontSize')
$("h1").css({
  'padding-left','20px'
})
```

prop、attr、removeProp、removeAttr方法

attr方法读写网页元素的属性，如a元素的href属性，img的src属性

prop方法读写DOM元素的属性，如nodeName，nodeType、tagName

removeProp方法移除某个DOM属性

removeAttr方法移除某个HTML属性

```javascript
$("a").prop("oldValue",1234).removeProp('oldValue')
$("a").removeAttr("title")
```

data方法

用于在一个DOM对象上存储数据

```javascript
$("body").data("foo",52);
$("body").data("foo")
```

### 与DOM相关的方法

Append、appendTo方法

用于将参数插入当前元素的尾部

用法

```javascript
$('div').append("<p>World</p>")
("<p>World</p>").appendTo("div")
//两种效果相同，<div>hello</div>变为<div>hello <p>World</p></div>
```

prepend、prependTo方法

将参数中的元素变成当前元素的第一个子元素

```javascript
$("p").prepend("hello")
$("<p></p>").prependTo("div")
//<p></p>变为<p>hello</p>
```

After、insertAfter方法

将参数中的元素插在当前元素的后面

```javascript
$("div").after("<p></p>")
$("<p></p>").insertAfter("div")
//两种效果相同，<div></div>变成<div></div><p></p>
```

before、insertBefore方法

将参数中的元素插入到当前元素的前面

```javascript
$("div").before("<p></p>")
$("<p></p>").insertbefore("div")
//两种效果相同，<div></div>变成<p></p><div></div>
```

wrap、wrapAll、unwrap、wrapInner方法

wrap方法将参数中的元素变成当前元素的父元素

unwrap方法移除当前元素的父元素

wrapAll方法为结果集的所有元素，添加一个共同的父元素

wrapInner方法为当前元素的所有子元素添加一个父元素

```javascript
$("p").wrap("<div></div>") //<p></p>变为<div><p></p></div>
$("p").unwrap()   //<div><p></p></div>变为<div></div>
$("p").wrapAll("<div></div>")  // <p></p><p></p>变为<div><p></p><p></p></div>
$("p").wrapInner('<strong></strong>')   //<p>hello</p>变为<p><strong>hello</strong></p>
```

clone方法

clone方法克隆当前元素

```javascript
var clones = $('li').clone();
```

remove、detach、replaceWith方法

remove方法移除并返回一个元素，取消该元素上所有事件的绑定

detach方法移除并返回一个元素，但不取消该元素上所有事件的绑定

replaceWith用参数中的元素替换并返回当前元素，取消当前元素的所有事件的绑定

```javascript
$('p').remove()
$('p').detach()
$('p').replaceWith('<div></div>')
```

### 遍历方法

first方法返回结果集的第一个成员，last方法返回结果集的最后一个成员

```javascript
//返回div元素内部第一个p元素和最后一个p元素
$("div p").first();
$("div p").last();
```

next方法紧邻的下一个同级元素，prev方法紧邻的上一个同级元素

```javascript
$("h2").next();
$("h2").prev();
```

siblings方法返回当前元素的所有同级元素，也可以设置参数来过滤对同胞元素的搜索，nextAll方法返回当前元素其后的所有同级元素，prevAll返回当前元素前面的所有同级元素

```javascript
//返回h2所有同胞元素下的所有p元素
$("h2").siblings("p")
$("h2").nextAll("p")
$("h2").prevAll("p")
```

parent方法返回当前元素的父元素，parents方法返回当前元素的所有上级元素，也可以指定元素类型进行筛选，parentsUntil方法返回介于两个给定元素之间的所有祖先元素

```javascript
$("span").parent()
$("span").parents("ul")
$("span").parentsUntil("div")
```

closest方法返回当前元素、以及当前元素的所有上级元素之中第一个符合条件的元素

find方法返回当前元素的所有符合条件的下级元素

```javascript
$("span").closest("ul").css({"color":"red"})

```

filter方法用于过滤结果集，他可以接受多种类型的参数，只返回与参数一致的结果，

not方法返回,与filter方法相反

```javascript
$("p").filter(".url")
$("p").not(".url")
```

has方法与filter方法作用相同，但是只过滤出子元素符号条件的元素

is方法判断当前jquery对象是否符合指定的表达式

hasClass方法判断当前对象所匹配的元素是否含有指定的css类名

```javascript
$('.object').is("expression")
$('.object').hasClass("className")
```

获取子元素是父元素的第几个子元素

```javascript
$(function(){
  $(".demo ul li").click(function(){
    var index=$(".demo ul li").index(this);
    console.log(index);
  })
})
```

获取第几个子元素

children方法获取该元素下的直接子集元素

find方法获取该元素下的所有子集元素

```javascript
//使用eq()方法找到
$('#test').children().eq(1).css({'display':'inline-block'})
$('#test').find('select').eq(2).val();
```

each方法用于遍历指定的对象和数组，并以对象的每个属性或者数组的每个成员作为上下文来遍历执行指定的函数，上下文是指通过this指针引用该元素

map方法用于处理当前的Jquery对象匹配的所有元素，并将结果封装为新的数组。

```javascript
$("li").each(function(){
  alert($(this))
})
```

### 网络请求

$.ajax()方法：执行异步AJAX请求，该方法常用于其他方法不能完成的请求

$.get()方法：使用AJAX的HTTP GET请求从服务器加载数据

$.getJSON()方法：使用HTTP GET请求从服务器加载JSON编码数据

$.getScript()方法：

$.load方法：从服务器加载数据，并把返回的数据放入被选元素中

$.post()方法：使用AJAX的HTTP POST请求从服务器加载数据

```javascript
$.ajax({url:"demo_test.txt",success:function(result){
  	$("#div1").html(result);
}})
```



### 使用this





### 动画

jquery提供简单的动画效果，但整体不如css强大

jquery提供以下动画效果：

show：显示当前元素

hide：隐藏当前元素

toggle：显示或隐藏当前元素

fadeIn：将当前元素的不透明度逐渐提升到100%

fadeOut：将当前元素的不透明度逐渐降为0%

fadeToggle：以逐渐透明或逐渐不透明的方式折叠显示当前元素

slideDown：以从上向下滑入的方式显示当前元素

slideDown只能显示被jquery隐藏的元素或者css设置为display：none的元素，使用overflow:hidden隐藏的元素不能被展示

slideUp：以从下向上滑出的方式隐藏当前元素

sildeToggle：以垂直滑入或滑出的方式折叠显示当前元素

这些方法可以不带参数使用，也可以使用毫秒或者预定义的关键字或自定义的关键字。预定义的关键字也可以进行修改。

```javascript
$('.hidden').show();
$('.hidden').show(300);
$('.hidden').show('slow');

//修改预定义关键字
jQuery.fx.speeds.fast = 50;
jQuery.fx.speeds.slow = 3000;
jQuery.fx.speeds.normal = 1000;

//使用自定义关键字
jQuery.fx.speeds.blazing = 30;

$('.hidden').show('blazing');
```

stop、delay方法

stop方法表示立即停止执行当前的动画，delay方法接受一个时间参数，表示暂停多少秒之后执行

```javascript
$('#stop').click(function(){
  $(".block").stop();
})
$("#foo").slideUp(300).delay(800).fadeIn(400)
```

animate

除了上面这些，jquery可以使用animate自己写动画效果。

animate接受三个参数，第一个参数是一个对象，表示动画结束时的css属性的值，第二个参数是动画持续的毫秒数。写css属性时，如果带有连字号，则需要使用骆驼拼写法改写；第三个参数表示动画结束时的回调函数

```javascript
$('div').animate({
 		left:'+=50';
  	opacity:0.25;
  	fontSize:'12px'
	},
  300,
  function(){
     console.log('done!');
	}
)
```

### 事件处理

on方法

on方法是jquery事件绑定的统一接口，事件绑定的那些简便方法都是on方法的简写形式，

on方法接受两个参数，第一个是事件名称，第二个是回调函数

```javascript
$('li').on('click',function(e){
    console.log($(this),text());
})
```

为li元素绑定click事件的回调函数

trigger方法

trigger方法用于触发回调函数，它的参数就是事件的名称

```javascript
$('li').trigger('click')
```

off方法

off方法用来移除事件的回调函数

```javascript
$('li').off()
```

### 插件





### jquery的其他应用

获取浏览器语言

```javascript
var type= navigator.appName;
if (type == "Netscape"){
  var lang = navigator.language;
}else{
  var lang = navigator.userlanguage;
};
var lang = lang.substr(0,2);
if(lang == "zh"){
   window.location.replace('url')
}else if(lang == "en"){
   window.location.replace('url')
}else{
   window.location.replace('url')
}
```

获取浏览器类型



## loash.js

### Array

_.chunk：将一个数组拆分成多个size长度的区块，并用这些区块组成一个新数组

_.compact：去掉原数组中所有的假值元素，创建一个新数组

_.concat：将所有参数组合，创建一个新数组，

_.difference：返回过滤后的新数组，去除重复值

_.differenceBy：

_.drop：去除数组中的前n个元素

_.dropRight：去除数组尾部的n个元素

_.dropRightWhile：

_.union: 创建一个按顺序排列的唯一值的数组, 可以合并数组。

_.unionWith: 这个方法类似[`_.union`](https://www.lodashjs.com/docs/lodash.unionWith#union)， 除了它接受一个 `comparator` 调用比较`arrays`数组的每一个元素。

_.unionBy: 这个方法类似[`_.union`](https://www.lodashjs.com/docs/lodash.unionBy#union) ，除了它接受一个 `iteratee` （迭代函数），调用每一个数组（`array`）的每个元素以产生唯一性计算的标准。

_.uniq(array)：创建一个去重之后的数组副本

_.uniqBy(array)：创建一个去重之后的数组副本

_.uniqWith(array)：创建一个去重之后的数组副本

_.zip()：创建一个分组元素的数组，数组的第一个元素包含所有给定数组的第一个元素，数组的第二个元素包含所有给定数组的第二个元素，以此类推。

_.zipWith()：这个方法类似于[`_.zip`](https://www.lodashjs.com/docs/lodash.zipWith#zip)，不同之处在于它接受一个 `iteratee`（迭代函数），来 指定分组的值应该如何被组合。 该iteratee调用每个组的元素

_.zipObject():

_.zipObjectDeep():

_.unzip()：这个方法类似于[`_.zip`](https://www.lodashjs.com/docs/lodash.unzip#zip)，除了它接收分组元素的数组，并且创建一个数组，分组元素到打包前的结构。

_.unzipWith()：此方法类似于[`_.unzip`](https://www.lodashjs.com/docs/lodash.unzipWith#unzip)，除了它接受一个`iteratee`指定重组值应该如何被组合。iteratee 调用时会传入每个分组的值

_.fromPairs()：接受数组，返回一个由键值对构成的对象

_.tail()：返回数组中去除第一个元素之后的数组

_.pull()：移除数组`array`中所有和给定值相等的元素

_.pullAll()：与pull方法功能相同，只是这个方法接收一个数组

_.pullAllBy(): 与pull方法功能相同，区别是这个方法接受一个 `iteratee`（迭代函数） 调用 `array` 和 `values`的每个值以产生一个值，通过产生的值进行了比较。

_.pullAllWith():这个方法类似于[`_.pullAll`](https://www.lodashjs.com/docs/lodash.pullAllWith#pullAll)，区别是这个方法接受 `comparator` 调用`array`中的元素和`values`比较。

_.pullAt():根据索引 `indexes`，移除`array`中对应的元素，并返回被移除元素的数组。

_.intersection():创建唯一值的数组，这个数组包含所有给定数组都包含的元素

_.intersectionBy(): 类似于intersection, 区别是它接受一个 `iteratee` 调用每一个`arrays`的每个值以产生一个值，通过产生的值进行了比较

_.intersectionWith():类似于intersection, 区别是它接受一个 `comparator` 调用比较`arrays`中的元素。结果值是从第一数组中选择

_.take(): 创建一个数组切片，从`array`数组的起始元素开始提取`n`个元素

_.takeRight(): 创建一个数组切片，从`array`数组的最后一个元素开始提取`n`个元素

_.takeRightWhile(): 从`array`数组的最后一个元素开始提取元素，直到 `predicate` 返回假值

_.takeWhile():  从`array`数组的起始元素开始提取元素，，直到 `predicate` 返回假值

_.xor(): 创建一个给定数组唯一值的数组

_.xorBy(): 除了它接受 `iteratee`（迭代器），这个迭代器 调用每一个 `arrays`（数组）的每一个值，以生成比较的新值

_.xorWith(): 该方法是像[`_.xor`](https://www.lodashjs.com/docs/lodash.xorWith#xor)，除了它接受一个 `comparator` ，以调用比较数组的元素

### Object

_.assign:返回一个对象

_.at:返回所选中对象属性组成的数组

_.findKey:返回满足value条件的key

_.findLastKey:

_.pick：返回一个从object中选中的属性的对象

_.pickBy：返回一个从object中判断为真的属性的对象

_.omit:与pick返回结果相反

_.omitBy：与pickBy返回结果相反

_.invert：返回一个原对象key-value倒置的对象

_.invertBy：返回一个原对象key-value倒置的对象，与invert不同的是value是一个数组

_.values：返回一个object可枚举属性组成的数组

_.valuesln：返回一个object自身和继承的可枚举属性组成的数组

_.toPairs: 创建一个`object`对象自身可枚举属性的键值对数组

_.toPairsIn: 创建一个`object`对象自身和继承的可枚举属性的键值对数组。

_.mapKeys: 创建一个对象，这个对象的value与`object`对象相同, 只修改对象中的key

_.mapValues: 创建一个对象，这个对象的key与`object`对象相同，值是通过 `iteratee` 运行 `object` 中每个自身可枚举属性名字符串产生的

_.merge: 返回对象的浅拷贝对象，对象自身和继承的可枚举属性到 `object` 目标对象。

_.mergeWith: 与merge函数功能相同，并且它接受一个 `customizer`，调用以产生目标对象和来源对象属性的合并值。如果`customizer` 返回 `undefined`，将会由合并处理方法代替

### String

_.camelCase：转换字符串为下驼峰写法

_.capitalize：返回首字母为大写、其他字母为小写的字符串

_.upperCase(): 转换字符串`string`为 空格 分隔的大写单词

_.upperFirst(): 转换字符串string的首字母为大写

_.lowerCase(): 转换字符串`string`以空格分开单词，并转换为小写

_.lowerFirst(): 转换字符串string的首字母为小写

_.snakeCase()：转换字符串为snakecase

_.startCase(): 转换字符串为start case

_.startWith: 返回布尔值，是否以该首字母开头

_.toLower：转换整个字符串的字符为小写

_.toUpper：转换整个字符串的字符为大写

_.trim:从字符串中移除前面和后面的空格或指定的字符串

_.trimEnd:移除字符串后面的空格或者指定字符串

_.trimStart: 移除字符串前面的空格或者指定字符串

_.pad(): 如果`string`字符串长度小于 `length` 则从左侧和右侧填充字符。 如果没法平均分配，则截断超出的长度

_.padEnd(): 如果`string`字符串长度小于 `length` 则在右侧填充字符。 如果超出`length`长度则截断超出的部分

_.padStart():如果`string`字符串长度小于 `length` 则在左侧填充字符。 如果超出`length`长度则截断超出的部分

_.truncate(): 截断`string`字符串，如果字符串超出了限定的最大值。 被截断的字符串后面会以 omission 代替，omission 默认是 "..."

### 常用函数



### Math

_.ceil：向上取整数

_.floor：向下取整数

_.max：取数组中的最大值

_.maxBy：

_.min：取数组中的最小值

_.minBy

_.mean：计算数组中的平均值

_.meanBy

_.divide：除



### Number

_.clamp：返回限制在上下限之间的值

_.inRange：返回布尔值，判断是否在上下限之间

_.random：产生一个上下限之间的数，可以指定是否为浮点数

```javascript
_.clamp(-10,-5,5) //5
_.inRange(3,2,4) //true
_.random(0,5)
```

## Radash

工具库

Radash 的特点是：

- 它是用 Typescript 编写的
- 源码中使用的语法更加的新
- 提供了一些 Lodash 没有的实用方法



## object-path

操作对象的库

```javascript
var obj = {
  a: {
    b: "d",
    c: ["e", "f"],
    '\u1200': 'unicode key',
    'dot.dot': 'key'
  }
};

var objectPath = require("object-path");
//get deep property
objectPath.get(obj, "a.b");  //returns "d"
objectPath.get(obj, ["a", "dot.dot"]);  //returns "key"
objectPath.get(obj, 'a.\u1200');  //returns "unicode key"

//get the first non-undefined value
objectPath.coalesce(obj, ['a.z', 'a.d', ['a','b']], 'default');
```



## byte.js

https://www.npmjs.com/package/bytes?activeTab=readme

https://github.com/visionmedia/bytes.js

格式化byte



## big.js



## bignumberjs



## decimaljs

`bignumber.js` 可能更适合金融类应用，因为用户不用担心丢失精度，除非使用了涉及除法的操作。

对于科学类应用来说，`decimal.js` 可能更好，因为它可以更有效的处理非常小或者非常大的数值。例如，它没有 `bignumber.js` 的限制，即当将一个小指数值和一个大指数值相加时，`bignumber.js` 将使用完全精度来操作，时间上来看不可行。

如上所述，`decimal.js` 还支持非整数次幂、三角函数、exp、ln、log 方法。这些额外的方法使得 `decimal.js` 比 `bignumber.js` 大得多。

- `big.js` 最小最简；便于使用；精度采用小数位；精度仅适用除法；4 种舍入模式。
- `bignumber.js` 配置选项；NaN；Infinity；精度采用小数位；精度仅适用于除法；随机数字；进制转换；9 种舍入模式；模模式；模幂运算。
- `decimal.js` 配置选项；NaN；Infinity；非整数次幂，exp，ln，log；三角函数；精度采用有效数字；所有操作均采取精度；随机数字；9 种舍入模式；模模式；二进制，八进制，十六进制；二进制指数符号



`bignumber.js` 和 `decimal.js` 也支持其它进制的计算，并支持前缀，比如十六进制的 `0x`。`decimal.js` 还可以使用二进制指数表示法处理二进制、八进制和十六进制



## fullpage.js

安装

```shell
npm install fullpage.js
```

## underscorejs

和loadsh一样，处理数组和对象的函数



## Velocity.js





## Momentjs

momentjs是js日期处理类库，js原生对date对象处理比较好，但是对时间戳处理不友好，所以moment在这方面比原生的时间处理函数更强大。

安装

```shell
npm install moment --save
```

引入

```javascript
import moment from 'moment'
```

日期格式化

```javascript
moment().format('MMMM Do YYYY, h:mm:ss a'); // 九月 19日 2020, 2:53:10 下午
moment().format('dddd');                    // 星期六
moment().format("MMM Do YY");               // 9月 19日 20
moment().format('YYYY [escaped] YYYY');     // 2020 escaped 2020
moment().format();                          // 2020-09-19T14:53:10+08:00
```

获取时间

```javascript
//获取以秒为单位的时间戳
moment().format('X')          // 返回值为字符串类型
moment().unix()               // 返回值为数值型
//获取以毫秒为单位的时间戳
moment().format('x')          // 返回值为字符串类型
moment().valueOf()            // 返回值为数值型
moment().get('year')          //获取年份
moment().get('11')            //获取月份
moment().get('25')            //获取某一天
moment().day() (0~6, 0: 周日, 6: 周六)
moment().weekday() (0~6, 0: 周日, 6: 周六)
moment().isoWeekday() (1~7, 1: 周一, 7: 周日)
moment().get('day')
mment().get('weekday')
moment().get('isoWeekday')
moment().toArray() // [years, months, date, hours, minutes, seconds, milliseconds] //获取当前年月日时分秒
moment().toObject() // {years: xxxx, months: x, date: xx ...} //获取当前年月日时分秒
moment().seconds()
moment().get('seconds')         //获取秒数
moment().minutes()
moment().get('minutes')         //获取分钟
moment().hours()
moment().get('hours')           //获取小时
```

获取相对时间

```javascript
//2020年9月19号15点为例
moment("20111031", "YYYYMMDD").fromNow(); // 9 年前
moment("20120620", "YYYYMMDD").fromNow(); // 8 年前
moment().startOf('day').fromNow();        // 15 小时前
moment().startOf('day')                   //获取今天0时0分0秒
moment().startOf('week')                  //获取本周第一天（周日）0时0分0秒
moment().startOf('isoWeek')               //获取本周周一0时0分0秒
moment().startOf('month')                 //获取当前月第一天0时0分0秒
moment().endOf('day').fromNow();          // 9 小时内
moment().endOf('day')                     //获取今天23小时59分59秒
moment().startOf('hour').fromNow(); 
```

日历时间

```javascript
//2020年9月19号15点为例
moment().subtract(10, 'days').calendar(); // 2020/09/09
moment().subtract(6, 'days').calendar();  // 上星期日14:36
moment().subtract(3, 'days').calendar();  // 上星期三14:36
moment().subtract(1, 'days').calendar();  // 昨天14:36
moment().calendar();                      // 今天14:36
//添加时间
moment().add(1, 'days').calendar();       // 明天14:36
moment().add(3, 'days').calendar();       // 下星期二14:36
moment().add(10, 'days').calendar();      // 2020/09/29
```

获取当前月总天数

```javascript
moment().daysInMonth()
```

比较时间

```javascript
let start_date = moment().subtract(1, 'weeks')
let end_date = moment()

end_date.diff(start_date) // 返回毫秒数

end_date.diff(start_date, 'months') // 0
end_date.diff(start_date, 'weeks') // 1
end_date.diff(start_date, 'days') // 7
start_date.diff(end_date, 'days') // -7
end_date.isBefore(start_date);  // 是否在某日期之前
end_date.isAfter(start_date); // 是否在日期之后
end_date.isSame(start_date);  // 日期是否相同
end_date.isSameOrBefore(start_date);  //是否在某日期之前或者相同
end_date.isSameOrAfter(start_date);  //是否在某日期之后或者相同
moment().isBetween(moment-like, moment-like); //是否在两个时间之间
moment().isLeapYear(); //是否是闰年
moment.isMoment(end_date); //是否是moment对象
moment.isDate(end_date);  //是否是原生date对象
```

转为js原生时间对象

```javascript
let m = moment()
let nativeDate1 = m.toDate()
```

设置时间

```javascript
moment().set('year', 2019)            //设置年份
moment().set('month', 11)             //设置月份
moment().set('date', 15)              //设置某个月中的某一天
moment().set('weekday', 0)            //设置日期为本周第一天（周日）
moment().set('isoWeekday', 1)         // 设置日期为本周周一
moment().set('hours', 12)             //设置小时
moment().set('minutes', 30)           //设置分钟
moment().set('seconds', 30)           //设置秒
```



https://segmentfault.com/a/1190000015240911

YYYY-MM-DD HH:MM 与YYYY-MM-DD HH:mm 区别：前者的分钟会和月数重叠，因此需要改成HH:mm

## date-fns

像lodash一样的操作日期

安装

```shell
npm install date-fns --save
# or with yarn
yarn add date-fns
```

使用

```javascript
import { compareAsc, format } from 'date-fns'

format(new Date(2014, 1, 11), 'yyyy-MM-dd')
//=> '2014-02-11'

const dates = [
  new Date(1995, 6, 2),
  new Date(1987, 1, 11),
  new Date(1989, 6, 10),
]
dates.sort(compareAsc)
//=> [
//   Wed Feb 11 1987 00:00:00,
//   Mon Jul 10 1989 00:00:00,
//   Sun Jul 02 1995 00:00:00
// ]
```



## dayjs

由于momentjs不再维护，因此寻找别的js时间库进行时间操作。

dayjs对时间的操作

dayjs大部分api都跟momentjs一致

### 一周

dayjs的一周默认是从周日到周六，因此如果用dayjs的`startOf('week')`和`endOf('week')`分别取到周日和周六，需要先判断选到的那天是周六还是周日，进行转换

```javascript
const time = dayjs(value).weekday() === 0 ? dayjs(value).subtract(1, 'day') : dayjs(value);

const startTime = time.startOf('week').add(1, 'day').format('YYYY-MM-DD 00:00:00');
const endTime = time.endOf('week').add(1, 'day').format('YYYY-MM-DD 23:59:59'),
```

### 格式化时间段





## luxon

安装

```shell
npm install --save luxon
```

使用

```javascript
dt = DateTime.now();
```



## bigNumberjs

bignumber.js是一个用于数学计算的库，支持任意精度

clone()

config()

maximum()、minimum()

random([])

实例方法：

plus():加法。minus():减法

.times():乘法    div():除法。.idiv():除法，只返回整数

.mod():取模/取余。.pow():指数运算  .sqrt():开平方

.comparedTo():比较大小

.dp() 精度调整

.integerValue()：取整

.sd()：有效数字

.toFixed()：返回字符串，截取小数点后位数

.toFormat()：数字格式化

.toNumber()：转换为js基础数值类型

## simple-statistics

对数组中的数据进行数学统计，获取中位数、平均值、众数等，以及求和等操作

https://github.com/simple-statistics/simple-statistics

## slidev

在线ppt制作



## shell.js

Shelljs是Node.js下的脚本语言解析器，具有丰富且强大的底层操作(Windows/Linux/OS X)权限。Shelljs本质就是基于node的一层命令封装插件，让前端开发者可以不依赖linux也不依赖类似于cmder的转换工具，而是直接在我们最熟悉不过的javascript代码中编写shell命令实现功能。

使用

```javascript
//局部模式
var shell = require('shelljs');
//全局模式下，就不需要用shell开头了。
//require('shelljs/global');

if (shell.exec('npm run build').code !== 0) {//执行npm run build 命令
  shell.echo('Error: Git commit failed');
  shell.exit(1);
}

//由于我的用另外一个仓库存放dist目录，所以这里要将文件增量复制到目标目录。并切换到对应目录。
shell.cp ('-r', './dist/*', '../../Rychou');
shell.cd('../../Rychou');

shell.exec('git add .');
shell.exec("git commit -m 'autocommit'")
shell.exec('git push')
```



## Robotjs自动化库

控制鼠标移动

```javascript
// Move the mouse across the screen as a sine wave.
var robot = require("robotjs");

// Speed up the mouse.
robot.setMouseDelay(2);

var twoPI = Math.PI * 2.0;
var screenSize = robot.getScreenSize();
var height = (screenSize.height / 2) - 10;
var width = screenSize.width;

for (var x = 0; x < width; x++)
{
	y = height * Math.sin((twoPI * x) / width) + height;
	robot.moveMouse(x, y);
}
```

控制键盘输入

```javascript
// Type "Hello World" then press enter.
var robot = require("robotjs");

// Type "Hello World".
robot.typeString("Hello World");

// Press enter.
robot.keyTap("enter");
```

获取屏幕数据

```javascript
// Get pixel color under the mouse.
var robot = require("robotjs");

// Get mouse position.
var mouse = robot.getMousePos();

// Get pixel color in hex format.
var hex = robot.getPixelColor(mouse.x, mouse.y);
console.log("#" + hex + " at x:" + mouse.x + " y:" + mouse.y);
```

## js-导出读取xlsx包

sheetjs

https://github.com/SheetJS/sheetjs

安装

```shell
npm install xlsx
```

读取本地文件

```javascript
// 读取本地excel文件
function readWorkbookFromLocalFile(file, callback) {
	var reader = new FileReader();
	reader.onload = function(e) {
		var data = e.target.result;
		var workbook = XLSX.read(data, {type: 'binary'});
		if(callback) callback(workbook);
	};
	reader.readAsBinaryString(file);
}
```

读取网络文件

```javascript
// 从网络上读取某个excel文件，url必须同域，否则报错
function readWorkbookFromRemoteFile(url, callback) {
	var xhr = new XMLHttpRequest();
	xhr.open('get', url, true);
	xhr.responseType = 'arraybuffer';
	xhr.onload = function(e) {
		if(xhr.status == 200) {
			var data = new Uint8Array(xhr.response)
			var workbook = XLSX.read(data, {type: 'array'});
			if(callback) callback(workbook);
		}
	};
	xhr.send();
}
```

通过上述代码获取到workbook对象

workbook对象中的`SheetNames`里面保存了所有的sheet名字，然后`Sheets`则保存了每个sheet的具体内容（我们称之为`Sheet Object`）。每一个`sheet`是通过类似`A1`这样的键值保存每个单元格的内容，我们称之为单元格对象（`Cell Object`）

`Sheet Object`表示一张表格，只要不是`!`开头的都表示普通`cell`，否则，表示一些特殊含义，具体如下：

 !ref：表示此sheet所有单元格的范围，如A1到F8

`[!merges]`：存放一些单元格合并信息，是一个数组，每个数组由包含`s`和`e`构成的对象组成，`s`表示开始，`e`表示结束，`r`表示行，`c`表示列；

每一个单元格是一个对象（`Cell Object`），主要有`t`、`v`、`r`、`h`、`w`等字段

- t：表示内容类型，`s`表示string类型，`n`表示number类型，`b`表示boolean类型，`d`表示date类型，等等
- v：表示原始值；
- f：表示公式，如`B2+B3`；
- h：HTML内容
- w：格式化后的内容
- r：富文本内容`rich text`

读取workbook对象

```javascript
// 读取 excel文件
function outputWorkbook(workbook) {
	var sheetNames = workbook.SheetNames; // 工作表名称集合
	sheetNames.forEach(name => {
		var worksheet = workbook.Sheets[name]; // 只能通过工作表名称来获取指定工作表
		for(var key in worksheet) {
			// v是读取单元格的原始值
			console.log(key, key[0] === '!' ? worksheet[key] : worksheet[key].v);
		}
	});
}
```

- `XLSX.utils.sheet_to_csv`：生成CSV格式
- `XLSX.utils.sheet_to_txt`：生成纯文本格式
- `XLSX.utils.sheet_to_html`：生成HTML格式
- `XLSX.utils.sheet_to_json`：输出JSON格式

导出xlsx文件

使用插件官方提供的工具类

- `aoa_to_sheet`: 这个工具类最强大也最实用了，将一个二维数组转成sheet，会自动处理number、string、boolean、date等类型数据；
- `table_to_sheet`: 将一个`table dom`直接转成sheet，会自动识别`colspan`和`rowspan`并将其转成对应的单元格合并；
- `json_to_sheet`: 将一个由对象组成的数组转成sheet；



处理单元格合并

```javascript
var aoa = [
	['主要信息', null, null, '其它信息'], // 特别注意合并的地方后面预留2个null
	['姓名', '性别', '年龄', '注册时间'],
	['张三', '男', 18, new Date()],
	['李四', '女', 22, new Date()]
];
var sheet = XLSX.utils.aoa_to_sheet(aoa);
sheet['!merges'] = [
	// 设置A1-C1的单元格合并
	{s: {r: 0, c: 0}, e: {r: 0, c: 2}}
];
openDownloadDialog(sheet2blob(sheet), '单元格合并示例.xlsx');
```

http://blog.haoji.me/js-excel.html#dao-chu-excel



## marked.js

marked.js可以将md解析为html

```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
```

使用

```javascript
let md = `### h3`
let table = marked(md);
```



## gray-matter

将对象转换为markdown文件

安装

```shell
npm install --save gray-matter
```

可以将字符串或者对象转换为文件

```javascript
{
  content: '<h1>Hello world!</h1>',
  data: { 
    title: 'Hello', 
    slug: 'home' 
  }
}
```

转换为

```markdown
---
title: Hello
slug: home
---
<h1>Hello world!</h1>
```

使用

```javascript
import matter = require('gray-matter');

// matter方法
const matter = require('gray-matter');
console.log(matter('---\ntitle: Home\n---\nOther stuff'));
//=> { data: { title: 'Home'}, content: 'Other stuff' }

// stringify方法
console.log(matter.stringify('foo bar baz', {title: 'Home'}));
// results in:
// ---
// title: Home
// ---
// foo bar baz

// read方法
const file = matter.read('./content/blog-post.md');
```



## Showdown.js

Markdown转 html

```html
<script src="/showdown-1.9.0/dist/showdown.min.js"></script>
<script type="text/javascript">
  var content = $('#text').text();
  var converter = new showdown.Converter();
  var htmlcontent = converter.makeHtml(content);
  $("#article").html(htmlcontent);
</script>
<body>
  <p id="text"></p>
  <p id="article"></p>
</body>
```



## markdoc

在js中转换markdown为html

安装

```shell
npm install @markdoc/markdoc
```

使用

```react
import Markdoc from '@markdoc/markdoc';

const doc = `
# Hello world.
> My first Markdoc page
`;

const ast = Markdoc.parse(doc);

const content = Markdoc.transform(ast);

const html = Markdoc.renderers.html(content);
```

https://markdoc.dev/docs/overview

## watermark-dom

`watermark.js`是基于DOM对象实现的BS系统的水印，确保系统保密性，安全性，降低数据泄密风险，简单轻量，支持多属性配置，动态计算水印，水印防被删（监听水印组件元素删除并重新添加，监听改变水印的属性并重新添加）。

使用

```javascript
watermark.init({ watermark_txt: "测试水印，1021002301，测试水印，100101010111101" });

watermark.load({ watermark_txt: "测试水印，1021002301，测试水印，100101010111101" });

watermark.remove();
```



## docsearchjs

首先在Algolia申请一个api。

在Vue中可以使用docsearchjs，配合Algolia使用

```shell
yarn add -D docsearch.js
```

在组件中使用

```Vue
<template>
	<input
    type="text"
    class="search-input"
   	id="search_input"
    placeholder=""
   />
</template>
<script>
import 'docsearch.js/dist/cdn/docsearch.min.css'
import docsearch from 'docsearch.js'
  
export default {
  mounted () {
    docsearch({
      apiKey: 'feb33c2506cdece7f0267859a856767a'
      IndexName: '',
      inputSelector: '#search_input',
      algoliaOptions: { 'fetchFilters': ['version: v2_0'] },
      debug: false
    })
  }
}
</script>
```

还有组件we-vue可以使用

## flexsearch

https://github.com/nextapps-de/flexsearch



其他vuepress配置

https://wiki.eryajf.net/pages/b205e9/#%E6%95%88%E6%9E%9C

## jsxss

`xss`是一个用于对用户输入的内容进行过滤，以避免遭受 XSS 攻击的模块

主要用于论坛、博客、网上商店等等一些可允许用户录入页面排版、格式控制相关的 HTML 的场景，`xss`模块通过白名单来控制允许的标签及相关的标签属性，另外还提供了一系列的接口以便用户扩展，比其他同类模块更为灵活。

特点：

- 白名单控制允许的 HTML 标签及各标签的属性
- 通过自定义处理函数，可对任意标签及其属性进行处理

安装

```shell
npm install xss
```

使用-Node

```javascript
var xss = require("xss");
var html = xss('<script>alert("xss");</script>');
console.log(html);
```

浏览器端使用

```html
<script src="https://rawgit.com/leizongmin/js-xss/master/dist/xss.js"></script>
<script>
  // 使用函数名 filterXSS，用法一样
  var html = filterXSS('<script>alert("xss");</scr' + "ipt>");
  alert(html);
</script>
```

也可以使用命令行进行转义

```shell
xss -i <源文件> -o <目标文件>
xss -i origin.html -o target.html
```

https://github.com/leizongmin/js-xss/blob/master/README.zh.md
