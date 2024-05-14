---
title: Javascript开发（十九）
date: 2021-01-18 21:40:33
categories: IT
tags:
    - IT，Web,Node
toc: true
thumbnail: http://cdn.kunkunzhang.top/javascript.png
---

​     第十九篇主要讲常用的js库.主要偏向于JavaScript的库和前端库

<!--more-->

## Protobufjs

在js中使用protobuf这种数据结构。protobuf.js是基于ByteBuffer.js的Protocol Buffers纯JavaScript实现，主要功能是解析.proto文件，构建Message类，编码解码。

安装

```shell
npm install protobufjs --save
```

protobuf.js 依赖 long.js、bytebuffer.js

创建.proto文件

```shell
vim msg.proto
```

编辑

```protobuf
syntax = "proto3";

package ns;

message Login {
    string name = 1;
    string pwd = 2;
}
message Address{
  required string province = 1;
  required string city = 2;
  required string country = 3;
}
```

使用

```javascript
let pbroot = require("protobufjs").Root;
let json = require("msg.json");
let root = pbroot.fromJSON(json);

let Message = root.lookupType("ns.Address");
```

加载

```javascript
import { load } from "protobufjs"; // respectively "./node_modules/protobufjs"

load("awesome.proto", function(err, root) {
  if (err)
    throw err;

  // example code
  const AwesomeMessage = root.lookupType("awesomepackage.AwesomeMessage");

  let message = AwesomeMessage.create({ awesomeField: "hello" });
  console.log(`message = ${JSON.stringify(message)}`);

  let buffer = AwesomeMessage.encode(message).finish();
  console.log(`buffer = ${Array.prototype.toString.call(buffer)}`);

  let decoded = AwesomeMessage.decode(buffer);
  console.log(`decoded = ${JSON.stringify(decoded)}`);
});
```

命令行工具

```javascript
var pbjs = require("protobufjs/cli/pbjs"); // or require("protobufjs/cli").pbjs / .pbts

pbjs.main([ "--target", "json-module", "path/to/myproto.proto" ], function(err, output) {
    if (err)
        throw err;
    // do something with output
});
```

## deepmerge

融合对象

安装

```shell
npm install deepmerge
```

使用

```javascript
const merge = require('deepmerge')

const x = {
    foo: { bar: 3 },
    array: [{
        does: 'work',
        too: [ 1, 2, 3 ]
    }]
}
 
const y = {
    foo: { baz: 4 },
    quux: 5,
    array: [{
        does: 'work',
        too: [ 4, 5, 6 ]
    }, {
        really: 'yes'
    }]
}
 
const output = {
    foo: {
        bar: 3,
        baz: 4
    },
    array: [{
        does: 'work',
        too: [ 1, 2, 3 ]
    }, {
        does: 'work',
        too: [ 4, 5, 6 ]
    }, {
        really: 'yes'
    }],
    quux: 5
}
 
merge(x, y) // => output

const foobar = { foo: { bar: 3 } }
const foobaz = { foo: { baz: 4 } }
const bar = { bar: 'yay!' }
 
merge.all([ foobar, foobaz, bar ]) // => { foo: { bar: 3, baz: 4 }, bar: 'yay!' }
```

## pluralize

安装

```shell
npm install pluralize --save
```

将任何单词复数和单数化

```javascript
pluralize('test') //=> "tests"
pluralize('test', 0) //=> "tests"
pluralize('test', 1) //=> "test"
pluralize('test', 5) //=> "tests"
pluralize('test', 1, true) //=> "1 test"
pluralize('test', 5, true) //=> "5 tests"
pluralize('蘋果', 2, true) //=> "2 蘋果"
 
// Example of new plural rule:
pluralize.plural('regex') //=> "regexes"
pluralize.addPluralRule(/gex$/i, 'gexii')
pluralize.plural('regex') //=> "regexii"
 
// Example of new singular rule:
pluralize.singular('singles') //=> "single"
pluralize.addSingularRule(/singles$/i, 'singular')
pluralize.singular('singles') //=> "singular"
 
// Example of new irregular rule, e.g. "I" -> "we":
pluralize.plural('irregular') //=> "irregulars"
pluralize.addIrregularRule('irregular', 'regular')
pluralize.plural('irregular') //=> "regular"
 
// Example of uncountable rule (rules without singular/plural in context):
pluralize.plural('paper') //=> "papers"
pluralize.addUncountableRule('paper')
pluralize.plural('paper') //=> "paper"
 
// Example of asking whether a word looks singular or plural:
pluralize.isPlural('test') //=> false
pluralize.isSingular('test') //=> true
```



## reconnecting-websocket

websocket重连包

```shell
npm install --save reconnecting-websocket
```

使用

```javascript
import ReconnectingWebSocket from 'reconnecting-websocket';
 
const urls = ['ws://my.site.com', 'ws://your.site.com', 'ws://their.site.com'];
let urlIndex = 0;
 
// round robin url provider
const urlProvider = () => urls[urlIndex++ % urls.length];
 
const rws = new ReconnectingWebSocket(urlProvider);
```

也可以这样

```javascript
import ReconnectingWebSocket from 'reconnecting-websocket';
import WS from 'ws';
 
const options = {
    WebSocket: WS, // custom WebSocket constructor
    connectionTimeout: 1000,
    maxRetries: 10,
};
const rws = new ReconnectingWebSocket('ws://my.site.com', [], options);
```

## arrow-js

响应式数据，可适配框架

```javascript
import { reactive, watch } from '@arrow-js/core'

const data = reactive({
  price: 25,
  quantity: 10,
  logTotal: true
})

watch(
  () => data.logTotal && data.price * data.quantity,
  (total) => total !== false && console.log(`Total: ${total}`)
)

data.price = 35
```



## octokit

使用javascript管理github restful api

octokitjs由GitHub维护 https://github.com/octokit/octokit.js

使用personal access token进行身份验证.首先，从 `octokit` 导入 `Octokit`。 然后，在创建 `Octokit` 实例时传递 personal access token

```javascript
import { Octokit } from "octokit";

const octokit = new Octokit({ 
  auth: 'YOUR-TOKEN',
});
```

如果要代表组织或其他用户使用 API，GitHub 建议使用 GitHub App。

导入 `App`，而不是从 `octokit` 导入 `Octokit`。 在以下示例中，将 `APP_ID` 替换为对应用 ID 的引用。 将 `PRIVATE_KEY` 替换为对应用私钥的引用。 将 `INSTALLATION_ID` 替换为要代表其进行身份验证的应用的安装 ID。 可以在应用的设置页面上找到应用的 ID 并生成私钥。

```javascript
import { App } from "octokit";

const app = new App({
  appId: APP_ID,
  privateKey: PRIVATE_KEY,
});

const octokit = await app.getInstallationOctokit(INSTALLATION_ID);
```

再github action中进行身份验证

如果要在 GitHub Actions 工作流中使用 API，则 GitHub 建议使用内置 `GITHUB_TOKEN` 进行身份验证，而不是创建令牌。 可以使用 `permissions` 密钥向 `GITHUB_TOKEN` 授予权限

如果使用 `run` 关键字在 GitHub Actions 工作流中执行 JavaScript 脚本，则可以将 `GITHUB_TOKEN` 的值存储为环境变量。 脚本可以作为 `process.env.VARIABLE_NAME` 访问环境变量

```yaml
- name: Run script
  env:
    TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    node .github/actions-scripts/use-the-api.mjs
```

工作流运行的脚本使用 `process.env.TOKEN` 进行身份验证

```javascript
import { Octokit } from "octokit";

const octokit = new Octokit({ 
  auth: process.env.TOKEN,
});
```

Octokit 支持多种请求方式。 如果知道终结点的 HTTP 谓词和路径，则可以使用 `request` 方法发出请求。 如果要利用 IDE 中的自动完成和键入功能，可以使用 `rest` 方法。 对于分页终结点，可以使用 `paginate` 方法来请求多页数据

若要使用 `request` 方法发出请求，请将 HTTP 方法和路径作为第一个参数传递。 将对象中的任何主体、查询或路径参数作为第二个参数传递。 例如，向 `/repos/{owner}/{repo}/issues` 发出 `GET` 请求并传递 `owner`、`repo` 和 `per_page` 参数

```javascript
await octokit.request("GET /repos/{owner}/{repo}/issues", {
  owner: "github",
  repo: "docs",
  per_page: 2
});
```

`request` 方法会自动传递 `Accept: application/vnd.github+json` 标头。 若要传递其他标头或不同的 `Accept` 标头，请将 `headers` 属性添加到作为第二个参数传递的对象。 `headers` 属性的值是将标头名称作为键并将标头值作为值的对象。 例如，发送值为 `text/plain`的 `content-type` 标头和值为 `2022-11-28` 的 `x-github-api-version` 标头

```javascript
await octokit.request("POST /markdown/raw", {
  text: "Hello **world**",
  headers: {
    "content-type": "text/plain",
    "x-github-api-version": "2022-11-28",
  },
});
```

每个 REST API 终结点在 Octokit 中都有一个关联的 `rest` 终结点方法。 为方便起见，这些方法通常会在 IDE 中自动完成。 可以将任何参数作为对象传递给该方法

```javascript
await octokit.rest.issues.listForRepo({
  owner: "github",
  repo: "docs",
  per_page: 2
});
```

参考：https://docs.github.com/zh/rest/guides/scripting-with-the-rest-api-and-javascript?apiVersion=2022-11-28

https://docs.github.com/zh/rest/guides/rendering-data-as-graphs?apiVersion=2022-11-28

## umami

GA替代品， 可以直接部署服务

https://umami.is/docs/getting-started

https://github.com/umami-software/umami



## handlebars.js

Handlebars 是一种简单的 **模板语言**。

它使用模板和输入对象来生成 HTML 或其他文本格式。Handlebars 模板看起来像常规的文本，但是它带有嵌入式的 Handlebars 表达式 。

Handlebars 表达式是一个 `{{`，一些内容，后跟一个 `}}`。执行模板时，这些表达式会被输入对象中的值所替换

Handlebars 内置的块助手代码 `each` 和 `with` 允许你更改当前代码块的值

```handlebars
<ul class="people_list">
  {{#with person}}
  	{{firstname}} {{lastname}}
  {{/with}}
  {{#each people}}
    <li>{{this}}</li>
  {{/each}}
</ul>
```

Handlebars.registerHelper 方法可以从模板中的任何上下文中访问 Handlebars 助手代码。

```handlebars
Handlebars.registerHelper('loud', function (aString) {
    return aString.toUpperCase()
})
```

代码块表达式使你可以自定义这样的助手代码：这个助手代码可以使用与当前上下文不同的上下文来调用模板。这些块助手代码在名称前 以 # 号标识，并且需要一个名称相同的结束模板 `/`。让我们考虑一个生成 HTML 列表的助手代码

```handlebars
{{#list people}}{{firstname}} {{lastname}}{{/list}}
```

js方法

```javascript
Handlebars.registerHelper("list", function(items, options) {
  const itemsAsHtml = items.map(item => "<li>" + options.fn(item) + "</li>");
  return "<ul>\n" + itemsAsHtml.join("\n") + "\n</ul>";
});
```

Handlebars 提供了额外的元数据，例如 Hash 参数来作为助手代码的最后一个参数

Hash 参数中的键必须为简单标识符，且值为 Handlebars 表达式。这意味着值可以为简单标识符，路径或字符串。

```javascript
{{link "See Website" href=person.url class="person"}}
Handlebars.registerHelper("link", function(text, options) {
    var attributes = [];

    Object.keys(options.hash).forEach(key => {
        var escapedKey = Handlebars.escapeExpression(key);
        var escapedValue = Handlebars.escapeExpression(options.hash[key]);
        attributes.push(escapedKey + '="' + escapedValue + '"');
    })
    var escapedText = Handlebars.escapeExpression(text);
    
    var escapedOutput ="<a " + attributes.join(" ") + ">" + escapedText + "</a>";
    return new Handlebars.SafeString(escapedOutput);
});
```

空格控制

```handlebars
{{#each nav ~}}
  <a href="{{url}}">
    {{~#if test}}
      {{~title}}
    {{~^~}}
      Empty
    {{~/if~}}
  </a>
{{~/each}}
```



## mustache.js

Js模版引擎

mustache.js 是 mustache 模板系统的 JavaScript 实现。

Mustache 是一套轻逻辑的模板语法。它可以用来处理 HTML 、配置文件、源代码等任何文件。它把模板中的标签展开成给定的数据映射或者对象中的属性值。

我们之所以说 “轻逻辑”，是因为模板里面没有 if 语句、else 语句或者 for 循环。只有模板标签。有的标签被替换成一个值，有的替换成空白，有的替换成一系列的值。

任何能够使用 JavaScript 的地方都可以使用 mustache.js 渲染模板。包括浏览器或者像 node、CouchDB 这样的服务器环境。

mustache.js 支持 CommonJS 和 AMD 规范。

使用

```html
<html>
<body onload="loadUser">
<div id="target">Loading...</div>
<script id="template" type="x-tmpl-mustache">
Hello ｛｛ name ｝｝!
</script>
</body>
</html>
<script>
  function loadUser() ｛
    var template = $('#template').html();
    Mustache.parse(template);   // optional, speeds up future uses
    var rendered = Mustache.render(template, ｛name: "Luke"｝);
    $('#target').html(rendered);
  ｝
</script>
```

文档地址：https://xn_0.gitee.io/2016/01/28/mustache-doc-cn/

github：https://github.com/janl/mustache.js

## html转pdf

### html2canvas + jspdf

https://juejin.cn/post/7012049739482923039#heading-2



### wkhtmltopdf

https://github.com/wkhtmltopdf/wkhtmltopdf



### iText

https://juejin.cn/post/7012050651999895565

模版页实践踩坑：

1. 对 `CSS` 样式支持极差，`CSS3` 属性都不支持
2. 不能用 `flex` 布局，基本只能用 `table` 实现
3. 要换页的地方，添加 `page-break-after: always;`，表示下一个元素将会换页，转 `pdf` 时 `itext` 会自动识别
4. 定位中，不支持以百分比为单位的 `css` 属性 `top`（`css property top in percents is not supported`）
5. 绝对定位可能报错（`Occupied area has not been initialized. Absolute positioning might be applied incorrectly`



## html转markdown

### turndown

https://github.com/mixmark-io/turndown

安装

```shell
npm install turndown
```



## html转canvas

### html2cavas

https://segmentfault.com/a/1190000009211079

https://github.com/niklasvh/html2canvas



### dom-to-image

https://github.com/tsayen/dom-to-image



## html转docx

### docx

```shell
npm install --save docx
```

使用

```javascript
import * as fs from "fs";
import { Document, Packer, Paragraph, TextRun } from "docx";

// Documents contain sections, you can have multiple sections per document, go here to learn more about sections
// This simple example will only contain one section
const doc = new Document({
    sections: [
        {
            properties: {},
            children: [
                new Paragraph({
                    children: [
                        new TextRun("Hello World"),
                        new TextRun({
                            text: "Foo Bar",
                            bold: true,
                        }),
                        new TextRun({
                            text: "\tGithub is the best",
                            bold: true,
                        }),
                    ],
                }),
            ],
        },
    ],
});

// Used to export the file into a .docx file
Packer.toBuffer(doc).then((buffer) => {
    fs.writeFileSync("My Document.docx", buffer);
});

// Done! A file called 'My Document.docx' will be in your file system.
```



## 中国法定节假日

https://github.com/NateScarlet/holiday-cn



## downloadjs

https://github.com/rndme/download

下载文件的npm包

安装

```shell
npm install downloadjs
```

使用

```shell
download("hello world", "dlText.txt", "text/plain");
```

## fileSaver.js

浏览器js根据blob数据创建文件

https://github.com/eligrey/FileSaver.js?tab=readme-ov-file

```javascript
var FileSaver = require('file-saver');
var blob = new Blob(["Hello, world!"], {type: "text/plain;charset=utf-8"});
FileSaver.saveAs(blob, "hello world.txt");

FileSaver.saveAs("https://httpbin.org/image", "image.jpg");

var canvas = document.getElementById("my-canvas");
canvas.toBlob(function(blob) {
    saveAs(blob, "pretty image.png");
});
```

## StreamSaverjs

大文件保存库，通过stream实现

https://github.com/jimmywarting/StreamSaver.js



## madge

查找依赖树

安装

```shell
npm -g install madge
```

在根目录下执行

```shell
madge ./src/index.js
```

